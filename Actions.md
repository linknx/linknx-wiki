# General

The type of the action is determined by the `type` attribute.  
Supported values for this attribute are:
- `set-value`
- `copy-value`
- `toggle-value`
- `set-string`
- `send-read-request`
- `cycle-on-off`
- `repeat`
- `conditional`
- `start-actionlist`
- `formula`
- `set-rule-active`
- `send-sms`
- `send-email`
- `dim-up`
- `shell-cmd`
- `ioport-tx`
- `script`
- `cancel` 

All actions can define an optional `delay` attribute defining the timespan that must elapse before the action is actually executed once it is triggered. The value is a number, optionally followed by a unit which is one of:
- `d` for days
- `h` for hours
- `m` for minutes
- `s` for seconds – this is the default unit if none is given
- `ms` for milliseconds – from 0.0.1.28 onwards

`send-sms`, `set-string`, `send-email`, `shell-cmd` and `ioport` actions can be parameterized with the value of one or several objects. To do so, their `var` attribute must be set to true to mark them as being parameterizable.
Then, some of their attributes can refer to object values by inserting object labels following the convention `${id_of_the_object}`. This applies to the following attributes:
- for `send-sms` actions: `id` and `value`
- for `set-string` actions: `value`
- for `send-email` actions: `to`, `subject` and `text`
- for `shell-cmd` actions: `cmd`
- for `ioport-tx` actions: `data`

Example:
```xml
<action type="ioport-tx" data="pl A1 xdim ${id_of_the_object}" ioport="X10" var="true"/>
```

***
THE TEXT BELOW NEEDS REVIEW

## set-value

The value defined by the _**value**_ attribute will be assigned to the object referred to by the _**id**_ attribute.  
**Example**
    
    <action type="set-value" id="kitchen_heating" value="comfort" />

## copy-value

This action can be used to copy an object's value to another object. The copy converts the value to a string then converts the string to the target object's value. This allows for example to copy an 8bit integer value into a floating point object or to a string object. 
    
    <action type="copy-value" from="heating_mode" to="prev_heating_mode"/>

## toggle-value

This action can be used for binary objects and is switching object's value between "on" and "off" once. 
    
    <action type="toggle-value" id="door_light"/>

## set-string

This action can set an object's value using a character string. The following pattern ${_obj_id_} will be replaced by the actual value of object _obj_id_ when the action is executed. 
    
    <action type="set-string" id="lcd_text" value="T° ext: ${ext_temp}°C" />

## send-read-request

This action can send a read request on the KNX bus. This is useful with objects that don't transmit their actual value periodically. This action will force the KNX device that has the "read" flag set in ETS to reply with a read response containing the actual value. If the linknx object has the "update" flag set, it will update its internal value with the received one. 
    
    <action type="send-read-request" id="gas_counter_value">

## cycle-on-off

Attribute _**id**_ reffering to the object to switch on and off.  
Attributes _**on**_ and _**off**_ defining the delay in seconds for on and off states.  
Attribute _**count**_ defining the number of on/off cycles to perform.  
An optional _**stopcondition**_ child element to abort the cycle when the condition is met. See [Condition's_Syntax] for details.  
**Example**
    
    <action type="cycle-on-off" id="closet_lights" on="5" off="5" count="10">

Please note that each cycle begins with switching the object to _**on**_ and ends with switching it to _**off**_. In result this action is always leaving the object finally in an "off" state. 

## repeat

Since version 1.28 there is a possibility to create a simple repetition loop that repeats a list of actions periodically a predefined number of times. All actions in the list are started at the same time, but you can delay some of them using the delay="x" parameter like on any action. 

Attribute _**period**_ defining the delay in seconds between repetitions.  
Attribute _**count**_ defining the number of repetitions to perform.  
One or more _**action**_ child elements to execute _**count**_ times every _**period**_.  
**Example**
    
    &lt;action type="repeat" period="2" count="3"&gt;
         &lt;action type="toggle-value" id="door_light"/&gt;
         &lt;action type="toggle-value" id="door_light" delay="500ms"/&gt;
    &lt;/action&gt;

## conditional

One _**condition**_ child elements that is evaluated when the action is executed.  
One or more _**action**_ child elements to execute if condition above is true.  
**Example**
    
    &lt;action type="conditional"&gt;
      &lt;condition type="object" id="alarm_email_enabled" value="on" /&gt;
      &lt;action type="send-email"  to="help@example.com" subject="Foreign contaminant"&gt;Intrusion in your shrubbery!&lt;/action&gt;
    &lt;/action&gt;

  
The condition doesn't allow use of _**trigger**_ attribute because the evaluation is triggered by the action execution.  
If parameter _**delay**_ is added to the &lt;action type="conditional"&gt; tag, the condition will be evaluated after the delay has elapsed. 

## start-actionlist

This special action can be used to force execution of the complete actionlist from a given rule. **Example**
    
    &lt;action type="start-actionlist" list="true" rule-id="flashing_lights" /&gt;

execute actions "if-true" or "on-true" of the rule, or: 
    
    &lt;action type="start-actionlist" list="false" rule-id="flashing_lights" /&gt;

to execute actions "if-false" or "on-false" of the rule 

## formula

Can be used to compute a value based on formula a*xm+b*yn+c. 

  * _x_ and _y_ are optional. If provided, they need to refer to a valid object. 
  * _a_, _b_ and _c_ are optional, too. They need to be valid (floating) numbers. The defaults for _a_ and _b_ are 1.0, the default value for _c_ is 0. 
  * _m_ and _n_ are optional, too. They need to be valid (floating) numbers. The defaults for both are 1.0. 

The result will be written to the _id_. 

#### Example
    
    &lt;action type="formula" id="object_id_result" n="1.0" m="2.0" c="3.0" b="4.0" a="5.0" y="object_id_y" x="object_id_x" &gt;&lt;/action&gt;

Formula&nbsp;: id=a*x^m+b*y^n+c =&gt; object_id_result = 5*object_id_x^2+4*object_id_y^1+3 

## set-rule-active

Can be used to to enable or disable (with optional parameter _active_ set to 'no') a given _rule-id_. 

  
**Example**
    
    &lt;action type="set-rule-active" active="no" rule-id="flashing_lights" /&gt;
    
    &lt;action type="set-rule-active" active="yes" rule-id="flashing_lights" /&gt;
    

## cancel

Attribute _**rule-id**_ defines the rule for which all pending actions will be cancelled.  
**Example**
    
    &lt;action type="cancel" rule-id="flashing_lights" /&gt;

Pending actions are the actions waiting for execution due to the _**delay**_ attribute, or action that spread over a period of time like dim-up or cycle-on-off. For these actions that spread over time, what has already been executed at the moment of cancel will not be undone. Only the processing that is not yet done will be skipped. Actions that are inside _**repeat**_ or _**conditional**_ actions can not be canceled if they are started. But the repetition itself, or the initial sleep due to _**delay**_ attribute can be interrupted. 

## send-sms

Attribute _**id**_ defines the phone number where to send SMS.  
Attribute _**value**_ defines the text of the message.  
Attribute _**from**_ defines the optional sender ID to be inclued in the message **Example**
    
    &lt;action type="send-sms"  id="32494123456" value="Foreign contaminant" /&gt;

## send-email

Attribute _**to**_ defines the e-mail's recipient.  
Attribute _**subject**_ defines the message subject.  
Element's text defines the e-mail body.  
**Example**
    
    &lt;action type="send-email"  to="help@example.com" subject="Foreign contaminant"&gt;Intrusion in your shrubbery!&lt;/action&gt;

  


## dim-up

Attribute _**id**_ refers to the dimmer value object to control. This object must be a scaling object (5.xxx, EIS6)  
Attributes _**start**_ and _**stop**_ define the dimming value (between 0 and 255) of starting and end point.  
Attribute _**duration**_ defines the number of seconds elapsed between starting and end point.  
**Example**
    
    &lt;action type="dim-up" id="dim_value_kitchen" start="0" stop="240" duration="1800" /&gt;

  


## shell-cmd

Attribute _**cmd**_ defines the shell command to execute.  
**Example**
    
    <action type="shell-cmd" cmd="killall -v eibd" />

## ioport-tx

See [IO_Ports] 

## script

See [Lua_Scripting] 
