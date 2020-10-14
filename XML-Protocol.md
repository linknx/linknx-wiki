THIS PAGE WAS IMPORTED FROM SOURCEFORGE – NEEDS REVIEW!

Applications or user-interfcaes who want to communicate with Linknx can do this using the XML protocol defined hereunder.  
Linknx support communication using a TCP connection or UNIX domain socket. The configuration is done in the "services" section of the configuration file.  
Once the communication is established, XML commands can be sent and answer received. Multiple commands can be sent successively over the same connection. For Linknx to be able to detect the end of a command, the command must end with the special ASCII character EOT (ASCII code 0x04, Ctrl-D in interactive terminal session). The problem is that it is not easy to send this character from a script. The easiest way to achieve this is to create a text file containing this character only, and append it to any information sent to linknx  


There are several types of commands: 

[TOC]

## read object

Read object commands are used to retrieve the current value of an object. The only parameter is "id" containing the object identifier. 
    
    Command:
    &lt;read&gt;&lt;object id="temp_bureau"/&gt;&lt;/read&gt;
    Answer:
    &lt;read status='success'&gt;20.2&lt;/read&gt;

It's also possible to read all or multiple objects with the following commands: 
    
    &lt;read&gt;&lt;objects/&gt;&lt;/read&gt;
    
    &lt;read&gt;&lt;objects&gt;&lt;object id="obj_id1"/&gt;&lt;object id="obj_id2"/&gt;&lt;object id="obj_id3"/&gt;...&lt;/objects&gt;&lt;/read&gt;

  


## write object

Write object commands are used to set the value of an object. The 2 mandatory parameters are "id" containing the object identifier and "value" containing the value to set. 
    
    Command:
    &lt;write&gt;&lt;object id="ecl_bureau" value="off"/&gt;&lt;/write&gt;
    Answer:
    &lt;write status='success'/&gt;

## read calendar

This command can be used to query for sunrise/sunset/noon time and for exception days: 
    
    
    &lt;read&gt;&lt;calendar month="9" day="30" /&gt;&lt;/read&gt;
    

## read config

Read config command is used to retrieve all or parts of Linknx's configuration.  
The syntax of configuration elements is the same as in the XML configuration file.  
Retrieve the complete configuration: 
    
    Command:
    &lt;read&gt;&lt;config/&gt;&lt;/read&gt;
    Answer:
    &lt;read status="success"&gt;
            &lt;config&gt;
                    &lt;objects&gt;
                            .....
                    &lt;/objects&gt;
                    &lt;rules&gt;
                            .....
                    &lt;/rules&gt;
                    &lt;services&gt;
                            .....
                    &lt;/services&gt;
            &lt;/config&gt;
    &lt;/read&gt;

Retrieve only rules (the same applies for objects or services): 
    
    Command:
    &lt;read&gt;&lt;config&gt;&lt;rules/&gt;&lt;/config&gt;&lt;/read&gt;
    Answer:
    &lt;read status="success"&gt;
            &lt;config&gt;
                    &lt;rules&gt;
                            .....
                    &lt;/rules&gt;
            &lt;/config&gt;
    &lt;/read&gt;

## write config

Write config command is used to add or update all or parts of linknx's configuration.  
The syntax of configuration elements is the same as in the XML configuration file.  
Add or update a rule: 
    
    Command:
    &lt;write&gt;&lt;config&gt;&lt;rules&gt;
            &lt;rule id="xxx"&gt;
                &lt;condition type="yyy"&gt;
                    .....
                &lt;/condition&gt;
                &lt;actionlist&gt;
                    .....
                &lt;/actionlist&gt;
            &lt;/rule&gt;
    &lt;/rules&gt;&lt;/config&gt;&lt;/write&gt;
    Answer:
    &lt;write status='success'/&gt;

If a rule with the same id already exists, it will be updated. If not, a new rule will be added.  
In case of update, the previous condition will be kept if no condition is specified.  
The same applies for action lists, but both lists (to execute when condition becomes true and false) will be cleared if one of them is specified.  
Delete a rule: 
    
    Command:
    &lt;write&gt;&lt;config&gt;&lt;rules&gt;
            &lt;rule id="xxx" delete="true"/&gt;
    &lt;/rules&gt;&lt;/config&gt;&lt;/write&gt;
    Answer:
    &lt;write status='success'/&gt;

Add or update an object: 
    
    Command:
    &lt;write&gt;&lt;config&gt;&lt;objects&gt;
            &lt;object id="cur_date" gad="1/1/151" flags="cwtuf" type="EIS4"&gt;Current Date&lt;/object&gt;
    &lt;/objects&gt;&lt;/config&gt;&lt;/write&gt;
    Result:
    &lt;write status='success'/&gt;

If an object with the same id already exists, it will be updated. If not, a new object will be added.  
In case of update, the object's internal state will be reset to the one specified by "init" parameter. In case init is missing, empty or equals "request", the previous internal state will be preserved.  
Delete an object: 
    
    Command:
    &lt;write&gt;&lt;config&gt;&lt;objects&gt;
            &lt;object id="xxx" delete="true"/&gt;
    &lt;/objects&gt;&lt;/config&gt;&lt;/write&gt;
    Answer:
    &lt;write status='success'/&gt;

Be careful not to delete objects still in use by some rules, there are actually no checks made internally to protect against that. 

(Re-)Configure a service: 
    
    Command:
    &lt;write&gt;&lt;config&gt;&lt;services&gt;
            &lt;emailserver type="smtp" host="smtp.anothermailserver.net:25" from="linknx@mydomain.com"/&gt;
    &lt;/services&gt;&lt;/config&gt;&lt;/write&gt;
    Answer:
    &lt;write status='success'/&gt;

There is no way to delete things here, but some services can be set to inactive by specifying no parameters for it. 

A "write config" command can contain one or more sections, each of them containing one or more elements. You can even set the entire config in one single command.  
There is no transaction mechanism yet. If a command containing multiple sections and/or elements is executed and returns an error, everything located before the error is processed and everything located after it is not. So for safety, it's perhaps better to split big commands in smaller parts. 

  


## execute action

Execute action command can be used to manually trigger execution of an action: 
    
    Command:
    &lt;execute&gt;&lt;action .../&gt;&lt;action .../&gt;&lt;/execute&gt;
    

  


## set rule active

Set rule active / inactive can be used as a special action to enable or disable triggering of some rules. 
    
    
    &lt;execute&gt;&lt;action type="set-rule-active" .../&gt;&lt;/execute&gt;
    

## read version string

This command can be used to retrieve the current version information, including information on optional features of linknx that can be enabled or disabled during compilation. **Example:**
    
    &lt;read&gt;&lt;version/&gt;&lt;/read&gt;

Should give response similiar to this: 
    
    &lt;read status="success"&gt;
        &lt;version&gt;
            &lt;value&gt;0.0.1.30&lt;/value&gt;
            &lt;features&gt;
                &lt;sms /&gt;
                &lt;e-mail /&gt;
                &lt;mysql /&gt;
                &lt;lua /&gt;
                &lt;log4cpp /&gt;
            &lt;/features&gt;
        &lt;/version&gt;
    &lt;/read&gt;

Here the version is 0.0.1.30 compiled with the following options enabled: sms, mysql, lua and log4cpp. 

## admin

This command is not yet implemented 