THIS PAGE WAS IMPORTED FROM SOURCEFORGE – NEEDS REVIEW!

Applications or user-interfcaes who want to communicate with Linknx can do this using the XML protocol defined hereunder.  
Linknx support communication using a TCP connection or UNIX domain socket. The configuration is done in the "services" section of the configuration file.  
Once the communication is established, XML commands can be sent and answer received. Multiple commands can be sent successively over the same connection. For Linknx to be able to detect the end of a command, the command must end with the special ASCII character EOT (ASCII code 0x04, Ctrl-D in interactive terminal session). The problem is that it is not easy to send this character from a script. The easiest way to achieve this is to create a text file containing this character only, and append it to any information sent to linknx  


There are several types of commands: 

[TOC]

## read object

Read object commands are used to retrieve the current value of an object. The only parameter is "id" containing the object identifier. 
    
    Command:
    <read><object id="temp_bureau"/></read>
    Answer:
    <read status='success'>20.2</read>

It's also possible to read all or multiple objects with the following commands: 
    
    <read><objects/></read>
    
    <read><objects><object id="obj_id1"/><object id="obj_id2"/><object id="obj_id3"/>...</objects></read>

  


## write object

Write object commands are used to set the value of an object. The 2 mandatory parameters are "id" containing the object identifier and "value" containing the value to set. 
    
    Command:
    <write><object id="ecl_bureau" value="off"/></write>
    Answer:
    <write status='success'/>

## read calendar

This command can be used to query for sunrise/sunset/noon time and for exception days: 
    
    
    <read><calendar month="9" day="30" /></read>
    

## read config

Read config command is used to retrieve all or parts of Linknx's configuration.  
The syntax of configuration elements is the same as in the XML configuration file.  
Retrieve the complete configuration: 
    
    Command:
    <read><config/></read>
    Answer:
    <read status="success">
            <config>
                    <objects>
                            .....
                    </objects>
                    <rules>
                            .....
                    </rules>
                    <services>
                            .....
                    </services>
            </config>
    </read>

Retrieve only rules (the same applies for objects or services): 
    
    Command:
    <read><config><rules/></config></read>
    Answer:
    <read status="success">
            <config>
                    <rules>
                            .....
                    </rules>
            </config>
    </read>

## write config

Write config command is used to add or update all or parts of linknx's configuration.  
The syntax of configuration elements is the same as in the XML configuration file.  
Add or update a rule: 
    
    Command:
    <write><config><rules>
            <rule id="xxx">
                <condition type="yyy">
                    .....
                </condition>
                <actionlist>
                    .....
                </actionlist>
            </rule>
    </rules></config></write>
    Answer:
    <write status='success'/>

If a rule with the same id already exists, it will be updated. If not, a new rule will be added.  
In case of update, the previous condition will be kept if no condition is specified.  
The same applies for action lists, but both lists (to execute when condition becomes true and false) will be cleared if one of them is specified.  
Delete a rule: 
    
    Command:
    <write><config><rules>
            <rule id="xxx" delete="true"/>
    </rules></config></write>
    Answer:
    <write status='success'/>

Add or update an object: 
    
    Command:
    <write><config><objects>
            <object id="cur_date" gad="1/1/151" flags="cwtuf" type="EIS4">Current Date</object>
    </objects></config></write>
    Result:
    <write status='success'/>

If an object with the same id already exists, it will be updated. If not, a new object will be added.  
In case of update, the object's internal state will be reset to the one specified by "init" parameter. In case init is missing, empty or equals "request", the previous internal state will be preserved.  
Delete an object: 
    
    Command:
    <write><config><objects>
            <object id="xxx" delete="true"/>
    </objects></config></write>
    Answer:
    <write status='success'/>

Be careful not to delete objects still in use by some rules, there are actually no checks made internally to protect against that. 

(Re-)Configure a service: 
    
    Command:
    <write><config><services>
            <emailserver type="smtp" host="smtp.anothermailserver.net:25" from="linknx@mydomain.com"/>
    </services></config></write>
    Answer:
    <write status='success'/>

There is no way to delete things here, but some services can be set to inactive by specifying no parameters for it. 

A "write config" command can contain one or more sections, each of them containing one or more elements. You can even set the entire config in one single command.  
There is no transaction mechanism yet. If a command containing multiple sections and/or elements is executed and returns an error, everything located before the error is processed and everything located after it is not. So for safety, it's perhaps better to split big commands in smaller parts. 

  


## execute action

Execute action command can be used to manually trigger execution of an action: 
    
    Command:
    <execute><action .../><action .../></execute>
    

  


## set rule active

Set rule active / inactive can be used as a special action to enable or disable triggering of some rules. 
    
    
    <execute><action type="set-rule-active" .../></execute>
    

## read version string

This command can be used to retrieve the current version information, including information on optional features of linknx that can be enabled or disabled during compilation. **Example:**
    
    <read><version/></read>

Should give response similiar to this: 
    
    <read status="success">
        <version>
            <value>0.0.1.30</value>
            <features>
                <sms />
                <e-mail />
                <mysql />
                <lua />
                <log4cpp />
            </features>
        </version>
    </read>

Here the version is 0.0.1.30 compiled with the following options enabled: sms, mysql, lua and log4cpp. 

## admin

This command is not yet implemented 
