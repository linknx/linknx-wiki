# A protocol for client-server interactions

Client applications or user interfaces who want to communicate with linknx can leverage the XML communication protocol to do so. This page is aimed at documenting its syntax.
Linknx can open an optional communication channel in the form a TCP or UNIX domain socket. The related configuration corresponds to the `<services/>` section of the [configuration](Configuration).
This channel can be used to send XML-formatted requests, to which linknx replies with a XML response. The format of such XML documents is described below.

A single connection can be reused to send multiple requests. The request document must end with an [End-of-Transmission character](https://en.wikipedia.org/wiki/End-of-Transmission_character) (ASCII code 0x04, Ctrl-D in an interactive terminal).


# Request content

## Read object

Read object commands are used to retrieve the current value of an object. It defines the single `id` attribute that contains the identifier of the object to get the value of.
    
Example:
```xml
<read><object id="temp_bureau"/></read>
Answer:
<read status='success'>20.2</read>
```

It is also possible to read several or all objects at once with the commands below.

To read all objects: 
```xml
<read><objects/></read>
```
    
To read several objects: 
```xml
<read><objects><object id="obj_id1"/><object id="obj_id2"/><object id="obj_id3"/>...</objects></read>
```

## Write object

Write object commands are used to set the value of an object. It must contain two attributes:
- `id` contains the object identifier
- `value` contains the value to set
    
Example:
```xml
<write><object id="ecl_bureau" value="off"/></write>
```

## Read calendar

This command is used to query sunrise/sunset/noon time and exception days.

Example:
```xml
<read><calendar month="9" day="30" /></read>
```

## Read config

The read config command retrieves all or parts of the current configuration.  
The syntax of configuration elements is the same as in the XML configuration file.  

Example to retrieve the whole configuration:
    
```xml
<read><config/></read>
```

The expected response from linknx looks like this:
```xml
<read status="success">
	<config>
		<objects>
			[...]
		</objects>
		<rules>
			[...]
		</rules>
		<services>
			[...] 
		</services>
	</config>
</read>
```

To limit the scope of the requested configuration to rules only:
```xml
<read><config><rules/></config></read>
```

The expected response from linknx:
```xml
<read status="success">
	<config>
		<rules>
			[...]
		</rules>
	</config>
</read>
```

Replace `<rules/>` with `<object/>` or `<services/>` in the sample above to retrieve configuration about objects or services.

## Write config

The write config command is used to set all or parts of the current configuration.
The syntax of configuration elements is the same as in the XML configuration file.  

The command can contain any number of sections, each of them containing any number of elements. It is even possible to define the entire config in one single command.
There is no transaction mechanism yet, so if a command returns an error, config parts that appear before the location that caused the error are processed and the remainder is not. To be on the safe side, it is usually good practice to to split large commands in several more atomic commands.

### Update a rule
```xml
<write>
	<config>
		<rules>
			<rule id="xxx">
				<condition type="yyy">
					[...]
				</condition>
				<actionlist>
					[...]
				</actionlist>
			</rule>
		</rules>
	</config>
</write>
```

The expected response from linknx:
```xml
<write status='success'/>
```

If a rule with the given id already exists, it is updated. Otherwise a new rule is be added to the configuration.
When updating the rule, the current condition is inserted in the new rule if none is specified.
The same applies for action lists, but both lists (to execute when condition becomes true and false) will be cleared if one of them is specified.  

### Delete a rule
```xml 
<write>
	<config>
		<rules>
            <rule id="xxx" delete="true"/>
    	</rules>
	</config>
</write>
```
 
Expected response from linknx:
```xml
<write status='success'/>
```

### Add or update an object
```xml
<write>
	<config>
		<objects>
			<object id="cur_date" gad="1/1/151" flags="cwtuf" type="EIS4">Current Date</object>
		</objects>
	</config>
</write>
```

Expected response from linknx:
```xml
<write status='success'/>
```

If an object with the given id already exists, it is updated. Otherwise, a new object is added.  
When updating, the object's internal state is reset according to the value of the `init` attribute. If the attribute is not defined, is empty or equals `request`, the internal state is left unchanged.  

### Delete an object
```xml
<write>
	<config>
		<objects>
            <object id="xxx" delete="true"/>
    	</objects>
	</config>
</write>
```

Expected response from linknx:
```xml
<write status='success'/>
```

Make sure not to delete objects that are referred to by rules. There are currently no checks to guard against this.

### Configure a service
```xml
<write>
	<config>
		<services>
			<emailserver type="smtp" host="smtp.anothermailserver.net:25" from="linknx@mydomain.com"/>
		</services>
	</config>
</write>

Expected response from linknx:
```xml
<write status='success'/>
```

The protocol does not provide any ways to delete a service. But some can be disabled by omitting all of their attributes. 

## Execute action

This type of command executes one or several actions defined with the same syntax as [the one used in rules](Actions). 

Example:
```xml 
<execute>
	<action .../>
	<action .../>
</execute>   
```

## Read version string

This command gets the current version information, including details on optional features that were enabled or disabled at compile time.

Example:
```xml  
<read>
	<version/>
</read>
```

On an instance of version 0.0.1.30, built with the SMS, MySQL, LUA and log4cpp options enabled, the expected answer from linknx is:
```xml    
<read status="success">
	<version>
		<value>0.0.1.30</value>
		<features>
			<sms/>
			<e-mail/>
			<mysql/>
			<lua/>
			<log4cpp/>
		</features>
	</version>
</read>
```

## Administration

This type of command is used to perform administrative operations on the running instance, such as:
- save the current configuration to an XML file
- register for notifications whenever a given object changes

This command is declared with a root `<admin/>` element, containing child element whose type depends on the actual admin task to perform.
The complete schema for this command is:
```xml
<admin>
	<save file="/path/to/config.xml"/>
	<notification>
		<register id="object_id"/>
		<unregister id="object_id"/>
		<registerall/>
		<unregisterall/>
	</notification>
</admin>
```

### Save configuration

To save the configuration that is currently loaded and active in the running instance of linknx, the command must declare a `<save/>` child element.
This element defines a unique attribute `file` which holds the path to the file to write. If not set, this attribute defaults to the file that was used to load the initial configuration from during startup.

The following example saves the configuration to the file `/etc/linknx/config.xml`:
```xml
<admin>
	<save file="/etc/linknx/config.xml"/>
</admin>
```

The expected response from linknx is:
```xml
<admin status="success"/>
```

### Notify of object changes

A client can include a `<notification/>` child element in the admin command if it wants to be notified of object changes. The `<notification/>` element itself can contain up to the four following types of elements:
- `<register id="object_id"/>` to subscribe to changes for object of id `object_id`
- `<unregister id="object_id"/>` to undo the effect of the above command
- `<registerall/>` to subscribe to changes on any of the objects
- `<unregisterall/>` to unsubscribe from all objects this connection subscribed to. No notifications will be sent after that point

When an object the connection has subscribed to undergoes a change, the connection is reused to send the following message to the client:
```xml
<notify id="object_id"
	<!-- object value here -->
</notify>
```
The format of the actual object value depends on the nature of the object.

Whatever the content of `<notification/>` is, the expected response from linknx is:
```xml
<admin status="success"/>
```
