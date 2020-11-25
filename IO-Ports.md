First IO ports support was introduced in version 0.0.1.26. Serial port were introduced in version 0.0.1.28.

# Configuration

Like other services, the configuration for IO ports lies in the `<services>` section of the [XML configuration](Services). Each port has an identifier that conditions and actions can refer to to send and receive data over it.  

## Sample configuration

```xml    
<ioports>
    <ioport id="irtrans" host="192.168.0.18" port="21000" rxport="21001"/>
    <ioport id="test-tcpcli" type="tcp" host="127.0.0.1" port="1030"/>
    <ioport id="test-serial" type="serial" dev="/dev/ttyS0" speed="9600" framing="8E1"/>
</ioports>
```

### Common attributes

- `id`: the mandatory IO port identifier used by conditions and actions.
- `type`: the port type. Either `udp` (default) or `tcp`.

### Attributes for `udp`

- `host`, `port`: resp. address and port to send data to. These attributes are only relevant when sending data and are thus optional. 
- `rxport`:local port used to listen to incoming udp data. Only relevant for receiving and thus optional.

### Attributes for `tcp` 

- `host`, `port`: resp. address and port to send data to. These attributes are only relevant when sending data and are thus optional. 
- `permanent`: when set to `true`, tell the IO port to keep its connection open after data was sent.

### Attributes for `serial` 

- `dev`: serial port device (e.g.: /dev/ttyS0) 
- `speed`: baudrate (200, 300, 600, 1200, 2400, 4800, 9600, 19200, 38400, 57600, 115200 or 23400) 
- `framing`: 3 characters (nb of data bits, parity and nb of stop bits; Default: "8N1" ) describing the framing settings. Number of data bits ranges from 5 to 8 included. Parity is E (even), O (odd) or N (none). Number of stop bits is 1 or 2. 
- `flow`: flow control setting. Value is one of `none` (default), `xon-xoff` and `rts-cts`. 

For the moment, the TCP io port can only connect as a client to external TCP servers. It's not possible for linknx to be the server allowing TCP clients to connect to it.  
The message delimitation is also not implemented, so the received data is transferred to conditions chunk-by-chunk as it is received. This can make a difference in case of big messages if they are fragmented in smaller parts. In future release, the massage delimitation (based on delimiter characters or timeouts) will be added. 

## Conditions

The following attributes can be used for ioport-conditions. 

  * ioport&nbsp;: the name (id) of the ioport the condition listens to 
  * expected&nbsp;: the value to compare with. Please take note of the remark in the Example below. 

## Actions

These are the attributes for action type="ioport-tx". 

  * ioport&nbsp;: the name (id) of the ioport to send data to 
  * data&nbsp;: the data to send 
  * hex&nbsp;: If "true", the data is sent as hexadecimal (default is "false"). 

## Example

Here's an example of rule that receives a message corresponding to an IR code from IRtrans infrared receiver/transmitter and send a sequence of ir commands to another device (use the red teletext button to power-on dvd player and play a dvd): 
```xml    
<rule id="start-dvd">
    <condition type="ioport-rx" expected="samsung,red" ioport="irtrans" trigger="true"/>
    <actionlist>
        <action type="ioport-tx" ioport="irtrans" data="snd dvd,power"/>
        <action type="ioport-tx" ioport="irtrans" data="snd dvd,menu" delay="5"/>
        <action type="ioport-tx" ioport="irtrans" data="snd dvd,enter" delay="7"/>
    </actionlist>
</rule>
```

There is currently a limitation on _**ioport-rx**_ conditions. Any message received and starting with the value of parameter _**expected**_ will trigger the condition. So in this case if the irtrans could send a message like "samsung,redisplay", it would also trigger the rule. 
