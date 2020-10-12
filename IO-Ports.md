THIS PAGE HAS BEEN IMPORTED FROM SOURCEFORGE – NEEDS REVIEW!
***

Since version 0.0.1.26, IO Ports support has been introduced. Support for serial port is introduced in 0.0.1.28. 

## Configuration

The configuration of io ports is located in the _**services**_ section. Each IO port has an identifier that can be used in conditions and actions to send and receive data over it.  
Here's an example of ioports configuration: 
    
    &lt;ioports&gt;
        &lt;ioport id="irtrans" host="192.168.0.18" port="21000" rxport="21001" /&gt;
        &lt;ioport id="test-tcpcli" type="tcp" host="127.0.0.1" port="1030" /&gt;
        &lt;ioport id="test-serial" type="serial" dev="/dev/ttyS0" speed="9600" framing="8E1" /&gt;
    &lt;/ioports&gt;

The supported parameters are: 

  * _**id**_&nbsp;: The IO port identifier (mandatory) used by conditions and actions 
  * _**type**_&nbsp;: The port type can be _**udp**_ or _**tcp**_. Default=_**udp**_

The other parameters are type-dependent.  
For port type _**udp**_: 

  * host&nbsp;: The destination used for sending udp data (optional, only needed for sending) 
  * port&nbsp;: The destination port used for sending udp data (optional, only needed for sending) 
  * rxport&nbsp;: The local port used to listen for incoming udp data (optional, only needed for reception) 

For port type _**tcp**_: 

  * host&nbsp;: The destination used for sending udp data (optional, only needed for sending) 
  * port&nbsp;: The destination port used for sending udp data (optional, only needed for sending) 
  * permanent&nbsp;: If "true", the connection will be kept open after sending data. If some conditions are configured, the connection is always kept open. 

For port type _**serial**_: 

  * dev&nbsp;: The serial port device (e.g.: /dev/ttyS0) 
  * speed&nbsp;: Baudrate (200, 300, 600, 1200, 2400, 4800, 9600, 19200, 38400, 57600, 115200 or 23400) 
  * framing&nbsp;: 3 characters (nb of data bits, parity and nb of stop bits; Default: "8N1" ) describing the framin settings. Nb of data bits range from 5 to 8 included. Parity is E (even), O (odd) or N (none). Nb of stop bits is 1 or 2. 
  * flow&nbsp;: Flow control setting (none, xon-xoff or rts-cts; Default: none). 

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
    
            &lt;rule id="start-dvd"&gt;
                &lt;condition type="ioport-rx" expected="samsung,red" ioport="irtrans" trigger="true"/&gt;
                &lt;actionlist&gt;
                    &lt;action type="ioport-tx" ioport="irtrans" data="snd dvd,power" /&gt;
                    &lt;action type="ioport-tx" ioport="irtrans" data="snd dvd,menu" delay="5"/&gt;
                    &lt;action type="ioport-tx" ioport="irtrans" data="snd dvd,enter" delay="7"/&gt;
                &lt;/actionlist&gt;
            &lt;/rule&gt;

There is currently a limitation on _**ioport-rx**_ conditions. Any message received and starting with the value of parameter _**expected**_ will trigger the condition. So in this case if the irtrans could send a message like "samsung,redisplay", it would also trigger the rule. 
