Each object element describes a communication object.
   + [Object definition main parameters](#object-definition-main-parameters)
   + [Object type](#object-type)
   + [Flags](#flags)
   + [Init](#init)
   + [Listener (child xml element)](#listener-child-xml-element)
   + [Log](#log)

# Object definition main parameters

The parameter "id" is mandatory and is used to identify the object in rules and by the XML based protocol.
Parameter "gad" is optional. If specified, the object will be linked to that group address on KNX bus. If not, the object will only be accessible in rules and in XML based protocol.

Here's a sample configuration:
```xml
<objects>
	<object type="1.001" id="alarm_active" init="persist">Alarm activated</object>
	<object type="11.001" id="cur_date" gad="1/1/151" flags="cwtuf">Current Date</object>
	<object type="10.001" id="cur_time" gad="1/1/150">Current Time</object>
	<object type="3.007" id="dim_living" gad="1/1/41">Living room dimmer</object>
	<object type="5.xxx" id="dim_value_living" gad="1/1/42">Living room dimmer value</object>
	<object type="20.102" id="heating_living" gad="1/1/70">Temperature controller mode for living room</object>
	<object type="1.001" id="light_office" gad="1/1/7">Office light</object>
	<object id="light_office_status" gad="1/2/7" init="request">Office light status</object>
	<object type="1.001" id="light_room1" gad="1/1/8">Bedroom 1 light</object>
	<object type="9.xxx" id="setpoint_living" gad="1/1/71">Setpoint temperature of living room</object>
	<object type="9.xxx" id="temp_living" gad="1/1/72">Actual temperature of living room</object>
</objects>

```

# Object type

Since version 0.0.1.25, the object definition uses Datapoint Types defined in KNX standard instead of EIS datatypes.
The old syntax is described in a [separate section](Object-definition-syntax-prior-to-0.0.1.25) for reference.

The "type" parameter can be one of

    1.001: switching (on/off) (EIS1)
    1.002: bool (after 0.0.1.30)
    1.003: enable (after 0.0.1.30)
    1.004: ramp (after 0.0.1.30)
    1.005: alarm (after 0.0.1.30)
    1.006: binary value (after 0.0.1.30)
    1.007: step (after 0.0.1.30)
    1.008: up/down (after 0.0.1.30)
    1.009: open/close (after 0.0.1.30)
    1.010: start (after 0.0.1.30)
    1.011: state (after 0.0.1.30)
    1.012: invert (after 0.0.1.30)
    1.013: dim send style (after 0.0.1.30)
    1.014: input source (after 0.0.1.30)
    2.001: switch (after 0.0.1.30)
    2.002: bool (after 0.0.1.30)
    2.003: enable (after 0.0.1.30)
    2.004: ramp (after 0.0.1.30)
    2.005: alarm (after 0.0.1.30)
    2.006: binary value (after 0.0.1.30)
    2.007: step (after 0.0.1.30)
    2.008: direction1 control (after 0.0.1.30)
    2.009: direction2 control (after 0.0.1.30)
    2.010: start (after 0.0.1.30)
    2.011: state (after 0.0.1.30)
    2.012: invert  (after 0.0.1.30)
    3.007: dimming (control of dimmer using up/down/stop) (EIS2)
    3.008: blinds (control of blinds using close/open/stop)
    4.001: char ASCII (after 0.0.1.30)
    4.002: char 8859_1 (latin1) (after 0.0.1.30)
    5.xxx: 8bit unsigned integer (from 0 to 255) (EIS6)
    5.001: scaling (from 0 to 100%)
    5.003: angle (from 0 to 360°)
    5.010: value 1 pulse (after 0.0.1.30)
    6.xxx: 8bit signed integer (EIS14)
    7.xxx: 16bit unsigned integer (EIS10)
    8.xxx: 16bit signed integer
    9.xxx: 16 bit floating point number (EIS5)
    9.001: temp: °C (after 0.0.1.30)
    9.002: tempd: °K (after 0.0.1.30)
    9.003: tempa: K/H (after 0.0.1.30)
    9.004: lux: lux (after 0.0.1.30)
    9.005: wsp: m/s (after 0.0.1.30)
    9.006: pressure: Pa (after 0.0.1.30)
    9.007: humidity: % (after 0.0.1.30)
    9.008: air quality: ppm (after 0.0.1.30)
    9.010: time: s (after 0.0.1.30)
    9.011: time: ms (after 0.0.1.30)
    9.020: volt: mV (after 0.0.1.30)
    9.021: current: mA (after 0.0.1.30)
    9.022: power density: W/m² (after 0.0.1.30)
    9.023: kelvin per percent: K/% (after 0.0.1.30)
    9.024: power: kW (after 0.0.1.30)
    9.025: volume flow: L/h (after 0.0.1.30)
    9.026: rain amount: L/m² (after 0.0.1.30)
    9.027: value temp f: °F (after 0.0.1.30)
    9.028: value wsp kmh: km/h  (after 0.0.1.30)
    10.001: time (EIS3)
    11.001: date (EIS4)
    12.xxx: 32bit unsigned integer (EIS11)
    13.xxx: 32bit signed integer
    14.xxx: 32 bit IEEE 754 floating point number
    16.000: string (max 14 ASCII chars restricted to ASCII codes 0..127) (EIS15)
    16.001: string (max 14 ASCII chars in range 0..255)
    20.102: heating mode (auto/comfort/standby/night/frost)
    28.001: variable length string object
    29.xxx: signed 64bit value
    232.600: rgb object 

It's optional and the default value is 1.001.

# Flags

The "flags" parameter works the same as in ETS. The value of each flag is represented by a character:
+ c: Communication (allow the object to interact with the KNX bus)
+ r: Read (allow the object to answer to a read request from another participant)
+ w: Write (update the object's internal value with the one received in write telegram if they are different)
+ t: Transmit (allow the object to transmit it's value on the bus if it's modified internally by a rule or via XML protocol)
+ u: Update (update the object's internal value with the one received in "read response" telegram if they are different)
+ f: Force (force the object value to be transmitted on the bus, even if it didn't change).In the recent versions you can use s : Stateless flag alternatively which means exactly the same (object does not update it's state so linknx should always send it's value to the bus
+ i: Init (useless for the moment. Will perhaps replace the parameter init="request" in the future)

Each of these characters set their corresponding flag when appearing in the flags attribute.
The flags attributes defaults to "cwtu" (Communication, Write, Transmit and Update), which should suit most cases. For example, objects like switches often represent the real switch state.
Another useful set of flags is "crwtf" (or "crwts") for objects which need to send their value to the KNX bus even when the value stored by linknx did not change. It can come handy for scenes: every time a scene object's value is set to 'on', its value needs to be sent to the bus KNX to make the magic happen.

# Init

The "init" parameter configures the initial object's value (at application startup). It can be any value considered valid for the object type or one of the special keywords "request" and "persist". "request" requests a read operation from the KNX bus when the value is first queried. "persist" tries to load a value from persistent storage (see persistence element in services section). The "init" attribute defaults to "request".
Please note that this behaviour can be altered by adding a Listener element as described below.

# Listener (child xml element)

Another optional xml element which can be used inside object definition. This element binds two or more different objects so that changes in one object are mirrored to the other object. This can be used for instance when there are two different group addresses, one for setting object value (set object), and another address for reading the current value of the object (status object).

```xml
<object id="gate_open" gad="0/1/0" init="request"><listener gad="4/1/0"/>Main gate</object>;
<object id="gate_status" gad="4/1/0" init="request">Main gate open/close status</object>
```

Please note that a listener object is added as a separate xml element inside an object definition. This allows to add more than one listener for each object. This situation arises when an object needs to change its value in response of more than one KNX telegram. For example:

```xml
<object id="lamp17" gad="0/1/0" init="request">
   <listener gad="4/1/0" read="true"/>
   <listener gad="4/1/99">
   Lamp number 17
</object>
<object id="lamp17_status" gad="4/1/0" init="request">Lamp17 status</object>
<object id="all_lamps" gad="4/1/99" init="request">All Lamps on_off</object>
```

The "lamp17" object's value can change with a telegram for lamp17 but also with one for all_lamps.
The optional 'read="true"' attribute states that the object should not fetch its value by sending a read request to the KNX bus. It must instead request listener object's value and copy it. This is used in scenarios where a device - a switch actuator for instance - participates in two different group addresses:
+ one to set the new value to the controlled switch actuator
+ another one to read this actuator's status object
This is true for most 16 port and 32 port switch actuator devices. So in case of such devices you should always create two separate objects per KNX device, connected by a listener with read="true" option.

# Log

The log parameter will cause linknx to dump the changes of the object's value to a file.

```xml
<object id="gate1_open" gad="0/1/8" flags="cwtuf" init="persist" log="true">Left hand side entry gate</object>
```

The above definition requires the gate1_open object's current value to be saved in the persistent storage file and updated upon every change. Moreover, the file /var/linknx/log/gate1_open.log will log every change done to the "0/1/8" group address with their corresponding timestamp.
Before enabling this log feature, make sure linknx has write permission in the destination logging folder.