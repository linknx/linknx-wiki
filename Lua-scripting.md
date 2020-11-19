Lua scripting was introduced in version 0.0.1.26.

Lua scripting commands can be used in actions and their conditions to have more flexibility over the definition of such actions.
Readers who are new to Lua should check out the [official website](https://www.lua.org) for reference documentation.

# Lua functions

In the context of Lua scripting embedded in linknx, the following functions are available.

## obj

`obj(object_id)`

Allows to access the current value of a linknx object (the value is returned as a string). Available in conditions and actions.

## isException

`isException(timestamp)`

Tells whether `timestamp` is an exception day. If no timestamp is given, the current time is used. Available in conditions.

## set

`set(object_id, value)`

Assigns `value` to the linknx object of id `object_id`. Available in actions.

## iosend

`iosend(ioport, data)`

Sends `data` to `ioport`. See also [IO-Ports](io-ports). Available in actions.

## sleep

`sleep(delay)`

Suspends the execution of the script for `delay` seconds. Available in actions.
