# General

The type and behavior of the action is determined by the `type` attribute.  
Supported values for this type are:
- [set-value](#set-value)
- [copy-value](#copy-value)
- [toggle-value](#toggle-value)
- [set-string](#set-string)
- [send-read-request](#send-read-request)
- [cycle-on-off](#cycle-on-off)
- [repeat](#repeat)
- [conditional](#conditional)
- [start-actionlist](#start-actionlist)
- [formula](#formula)
- [set-rule-active](#set-rule-active)
- [send-sms](#send-sms)
- [send-email](#send-email)
- [dim-up](#dim-up)
- [shell-cmd](#shell-cmd)
- [ioport-tx](#ioport-tx)
- [script](#script)
- [cancel](#cancel)

Each type comes with its own set of attributes. See below for more details about each type of action.
But all actions have an optional `delay` attribute defining the time span that must elapse before the action is actually executed once it is triggered. The value is a number, optionally followed by a unit which is one of:
- `d` for days
- `h` for hours
- `m` for minutes
- `s` for seconds – this is the default unit if none is given
- `ms` for milliseconds – from 0.0.1.28 onwards

## Parameterization
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

# set-value

When executed, the action assigns the object referred to by the `id` attribute a constant value defined by the `value` attribute

## Example:
```xml
<action type="set-value" id="kitchen_heating" value="comfort"/>
```
This action sets the value of the object `kitchen_heating` to the string value `comfort`.

# copy-value

This action assigns an object the value from a reference object. During the assignment process, the reference value is first converted to a string and then converted again to the type of the targeted object. Doing so, it is for example possible to copy an 8-bit integer value to a floating point object or to a string object.

## Example    
```xml
<action type="copy-value" from="heating_mode" to="prev_heating_mode"/>
```

# toggle-value

This action is able to work with binary objects only. If the object has a value set to `on`, the action switches it to `off` and vice versa.

## Example    
```xml
<action type="toggle-value" id="door_light"/>
```

# set-string

This action sets the value of a targeted string object using a predefined format. The format can mix constant string portions with references to other objects using the pattern `${obj_id}`. When the action is executed, such reference is  replaced with the current value of the object which identifier is `obj_id`.

## Example    
```xml
<action type="set-string" id="lcd_text" value="T° ext: ${ext_temp}°C"/>
```

# send-read-request

This action sends a read request to a group address on the KNX bus. This is useful to compensate for objects that do not transmit their actual value periodically. This action forces the KNX device with "read" flag set in the targeted group to reply with a read response containing the current value of the group. If the object has the "update" flag set, it will update its internal value with one provided in the KNX response telegram.

## Example    
```xml
<action type="send-read-request" id="gas_counter_value"/>
```

# cycle-on-off

This type of action cyclically toggles the value of a targeted binary object identified with the value of the `id` attribute, following the algorithm below:
- set the value to `on`
- pause for a time span defined by the `on` attribute
- set the value to `off`
- pause for a time span defined by the `off` attribute
- start this cycle over, until `count` cycles have been executed

As a consequence, the first cycle starts off by setting the object to `on`. The last cycle ends with setting the object to `off`.

An optional `<stopcondition/>` child element can be used to abort the cycles if the condition is met before `count` is reached. The syntax of this condition follows the principles of [any regular condition](/linknx/linknx/wiki/conditions) for actions.  

## Example
```xml
<action type="cycle-on-off" id="closet_lights" on="5" off="5" count="10">
    <stopcondition type="object" id="cancel-blinking" value="on"/>
</action>
```

# repeat

Version 0.0.1.28 introduced the ability to repeat a series of actions for a predefined number of cycles. By default, actions in the list are executed all at once, but it is easy to leverage the standard `delay` attribute common to all types of actions to create a sequence of actions.

This type of action is defined as follows:
- a `period` attribute defines the pause in seconds between repetitions
- a `count` attribute defines the total number of repetitions to execute
- one or several `<action/>` child elements to execute cyclically

## Example
```xml    
<action type="repeat" period="2" count="3">
    <action type="toggle-value" id="door_light"/>
    <action type="toggle-value" id="door_light" delay="500ms"/>
</action>
```

# conditional

When executed, this type of action executes one or several child actions if a condition is met. Otherwise, child actions are not executed.
The condition is defined in a `<condition/>` child element, as per the [condition syntax](/linknx/linknx/wiki/conditions).
The actions are defined as `<action/>` child elements.

## Example
```xml    
<action type="conditional">
    <condition type="object" id="alarm_email_enabled" value="on"/>
    <action type="send-email"  to="help@example.com" subject="Foreign contaminant">Intrusion in your shrubbery!</action>
</action>
```
  
Note: The condition element does not support the usual `trigger` attribute because the evaluation of child actions is always triggered as a consequence of the `conditional` action. The `trigger` attribute would not make sense in that context.
If a `delay` is set to `<action type="conditional"/>` element, the condition is assessed when the delay has expired.

# start-actionlist

This action forces execution of the actions defined in the `<actionlist/>` of a targeted rule, just as if those actions were children of the `start-actionlist` action.

This action is defined via two attributes:
- `rule-id` contains the identifier of the rule whose actions are to be executed when this action is executed
- `list` contains a value that tells which action lists of the targeted rule are executed. It accepts one of the following two values:
    * `true` to execute action lists `on-true` and `if-true`. This is the default.
    * `false` to execute action lists `on-false` and `if-false`

## Example

Action which executes actions `if-true` **and** `on-true` of the rule `flashing_lights`. 
```xml    
<action type="start-actionlist" list="true" rule-id="flashing_lights"/>
```

Action which executes actions `if-false` **and** `on-false` of the rule `flashing_lights`. 
```xml    
<action type="start-actionlist" list="false" rule-id="flashing_lights"/>
```

# formula

This type of action evaluates a formula and sets a targeted numerical object value to the computed result. The formula is of the form `a*X^m + b*Y^n + c`, with:
- `X` and `Y` being values of objects, evaluated at execution time. Those two objects are referenced with their identifiers provided by the optional attributes `x` and `y`. If `x`(resp. `y`) is undefined, `X`(resp. `Y`) is set to zero.
- `a`, `b` and `c` are optional floating point constant numbers provided by their homonymous attributes. Constants `a` and `b` default to `1.0` and `c` default to `0.0`.
- `m` and `n` are optional floating point power exponents whose values are provided by their homonymous attributes. Their default value is `1.0`.

The object which is assigned the computation result is referenced with its identifier stored in the `id` attribute.

## Example

The action representing the pseudo formula `id = a*x^m + b*y^n + c` => `object_id_result = 5*object_id_x^2 + 4*object_id_y^1 + 3` is defined with:
```xml
<action type="formula" id="object_id_result" n="1.0" m="2.0" c="3.0" b="4.0" a="5.0" y="object_id_y" x="object_id_x"/>
```

# set-rule-active

This type of action enables or disables a targeted rule. This action is defined with the following attributes:
- `rule-id` contains the identifier of the rule to control
- `active` is an optional parameter telling whether the rule should be disabled rather than enabled. When equal to one of `no`, `false` or `off`, the targeted rule is disabled. Otherwise it is enabled (this is the default behavior).

## Note

Disabling a rule does not cancel any of its currently running actions. It only _prevents_ the rule from triggering new action executions.
Similarly, enabling a disabled rule does not necessarily trigger any of its actions. It only _allows_ the rule to execute actions if its usual criteria based on its condition and its optional trigger are met.

## Example

Action disabling the `flashing_lights` rule, so that it cannot execute any of its actions anymore:
```xml
<action type="set-rule-active" active="no" rule-id="flashing_lights"/>
```

Action enabling the `flashing_lights` rule, so that it becomes ready to execute its actions again:
```xml    
<action type="set-rule-active" active="yes" rule-id="flashing_lights"/>
```

# cancel

This type of action cancels the pending actions of a targeted rule. Cancelable actions fall into two categories:
- actions which are waiting for their execution delay to be consumed after their parent rule has been triggered. Those correspond to actions which define a `delay` attribute and which have not yet started their execution
- actions whose execution stretches over a period of time, such as [dim-up](#dim-up) or [cycle-on-off](#cycle-on-off). If such actions are cancelled while their execution is already ongoing, the operations that have already been done will not be automatically undone but the remaining operations that have not yet been applied will be skipped. Actions which are part of [repeat](#repeat) or [conditional](#conditional) actions cannot be canceled once they are running but the repetition itself can be aborted

This type of action defines a `rule-id` attribute which holds the identifier of the rule to cancel.

## Example
```xml    
<action type="cancel" rule-id="flashing_lights"/>
```

# send-sms

This action sends a text message via the configured SMS gateway in the [services](Services) section of the configuration file. If no gateway is defined, an error will be raised when execution such actions.

This type of action requires two attributes to be defined:
- `id` defines the phone number to text
- `value` contains the message to send

Additionally, the `var` attribute can be set to `true` (`false` is the default) to activate the support for text message [parameterization](#parameterization).

## Example
```xml
<action type="send-sms"  id="32494123456" value="Foreign contaminant"/>
```

***
THE TEXT BELOW NEEDS REVIEW

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
