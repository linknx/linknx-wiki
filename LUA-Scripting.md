THIS PAGE WAS IMPORTED FROM SOURCEFORGE – NEEDS REVIEW!
***

Since version 0.0.1.26, Lua scripting has been introduced. 

Lua script is very simple and lightweight. Have a look at the [Lua 5.1 reference manual](http://www.lua.org/manual/5.1/) for further info. 

[TOC]

## Condition

A script condition is a LUA script that is executed when the rule is evaluated. The return value is interpreted as a boolean. 
    
    &lt;condition type="script"&gt;
    return tonumber(obj("setpoint_room1")) &gt; tonumber(obj("temp_room1"));
    &lt;/condition&gt;

In versions before 0.0.1.28, the condition is evaluated twice. So be careful if you reuse variables from one execution to the next one, or if you display some text with lua print function, it will be executed twice. This bug is corrected since 0.0.1.28. 

## Action

A script action is a LUA script that is executed when the action is executed. 
    
    &lt;action type="script"&gt;
    delta = obj("setpoint_room1")-obj("temp_room1");
    value = math.min(math.max(100*delta, 255),0);
    print ("value = ", value);
    set("heat_room1", value);
    &lt;/action&gt;

## LUA functions

The following functions are available in LUA-scripts. 

### obj

The function obj(object_id) allows to access the current value of a linknx object (the value is returned as a string). Available in Conditions and Actions. 

### isException

Check if timestamp "ts" is an exception day. If no timestamp is given, the current time is used. Available in Conditions. 

### set

set(object_id, value) sets the current value of a linknx object to value. Available in Actions. 

### iosend

iosend(ioport, data) sends "data" to "ioport". See also [IO_Ports]. Available in Actions. 

### sleep

sleep(delay) suspends LUA script execution for "delay" seconds. Available in Actions. 
