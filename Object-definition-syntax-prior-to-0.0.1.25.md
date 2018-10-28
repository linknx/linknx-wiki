***
**DEPRECATED SINCE 0.0.1.25**
***

New syntax is described in the [Objects Definition Section](Objects).
```xml
<objects>
	<object id="alarm_active" init="persist">Alarm activated</object>
	<object id="heating_living" gad="1/1/70" type="heat-mode">Temperature controller mode for living room</object>
	<object id="cur_date" gad="1/1/151" forcewrite="true" type="EIS4">Current Date</object>
	<object id="cur_time" gad="1/1/150" type="EIS3">Current Time</object>
	<object id="dim_living" gad="1/1/41" type="EIS2">Living room dimmer</object>
	<object id="dim_value_living" gad="1/1/42" type="EIS6">Living room dimmer value</object>
	<object id="light_office" gad="1/1/7">Office light</object>
	<object id="light_room1" gad="1/1/8">Bedroom 1 light</object>
	<object id="setpoint_living" gad="1/1/71" type="EIS5">Setpoint temperature of living room</object>
	<object id="temp_living" gad="1/1/72" type="EIS5">Actual temperature of living room</object>
</objects>
```

Each object element describes a communication object.
The *id* parameter is mandatory. It identifies the object in rules and in the XML based protocol.

The *gad* parameter is optional. If specified, the object is bound to that group address on the KNX bus. If omitted, the object is accessible only in rules and in the XML based protocol, with no direct interaction with the bus.

The *type* parameter is one of:
- EIS1: switching (on/off)
- EIS2: dimming (control of dimmer using up/down/stop)
- EIS3: time
- EIS4: date
- EIS5: value (floating point number)
- EIS6: scaling (integer from 0 to 255)
- heat-mode: heating mode (comfort/standby/night/frost)
- EIS15: string (max 14 ASCII char)

This *type* parameter is optional and defaults to EIS1.

The *flags* parameter is similar to the ETS flags. The value of each flag is represented by a letter:
c : Communication (allow the object to interact with the KNX bus)
r : Read (allow the object to answer to a read request from another participant)
w : Write (update the object's internal value with the one received in write telegram if they are different)
t : Transmit (allow the object to transmit it's value on the bus if it's modified internally by a rule or via XML protocol)
u : Update (update the object's internal value with the one received in "read response" telegram if they are different)
f : Force (force the object value to be transmitted on the bus, even if it didn't change)
i : Init (useless for the moment. Will perhaps replace the parameter init="request" in the future)
Each letter appearing inside the value of this parameter means the corresponding flag is set.

The *flags* parameter is optional and defaults to "cwtu" (Communication, Write, Transmit and Update).

The *init* parameter configures the initial object value at application startup. It can be any valid value for the object or one of the special keywords *request* and *persist*.
- *request*: the object issue a read operation on the KNX bus when its value is queried for the first time.
- *persist*: the object tries to load a value from persistent storage (see the *persistence* element in the services section).

The *init* parameter is optional and defaults to "request".

Finally, a parameter named *forcewrite* (which was deprecated in 0.0.1.20 and replaced with the *f* flag) can be used with value "true" to force write operation on KNX bus, even if the internal state is unchanged. It can be useful when actuators change state without giving any status feedback.