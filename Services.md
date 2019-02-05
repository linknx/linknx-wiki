This page details the Services section of linknx's [configuration](Configuration).

Here is an example of such section:
```xml
<services>
	<smsgateway type="clickatell" user="xyz" pass="xxx" api_id="123456" />
	<emailserver type="smtp" host="smtp.myprovider.com:25" from="linknx@mydomain.com" />
	<xmlserver type="inet" port="1028" />
	<knxconnection url="ip:192.168.0.10" />
	<persistence type="file" path="/var/lib/linknx/persist"/>
	<exceptiondays>
		<date day="1" month="1" />
		<date day="1" month="5" />
		<date day="21" month="7" />
		<date day="15" month="8" />
		<date day="1" month="11" />
		<date day="11" month="11" />
		<date day="25" month="12" />
		<date day="9" month="4" year="2007" />
		<date day="17" month="5" year="2007" />
		<date day="28" month="5" year="2007" />
		<date day="24" month="3" year="2008" />
		<date day="1" month="5" year="2008" />
		<date day="12" month="5" year="2008" />
		<date day="13" month="4" year="2009" />
		<date day="21" month="5" year="2009" />
		<date day="1" month="6" year="2009" />
		<date day="5" month="4" year="2010" />
		<date day="13" month="5" year="2010" />
		<date day="24" month="5" year="2010" />
	</exceptiondays>
	<location lon="21.01" lat="52.15"/>
	<ioports>
		<ioport host="192.168.0.18" port="21000" rxport="21001" id="irtrans"/>
	</ioports>
</services>
```

The smsgateway element configures the SMS service in charge of sending text messages to mobile phones. Currently, only the Clickatell HTTP API is implemented and requires libcurl to work. The required parameters are type (currently only "clickatell" supported), user, pass and api_id. An optional parameter "from" allows you to configure the sending number.

The emailserver element configures email service via an external SMTP server. Required parameters are type (only "smtp" supported), host and from. The host parameter must contain server name AND port number. This feature requires libesmtp to work.
SMTP authentication (optional) can be configured by adding login="xxxx" and pass="yyy" in config of emailserver.

The xmlserver element configures the interface to access linknx using the XML based protocol. The type parameter can be "inet" or "unix".

    For "inet", the port parameter is the TCP port the server will listen on (default = 1028).
    For "unix", the path parameter must contain the unix socket path (default /tmp/xmlserver.sock) [before linknx-0.0.1.30 the ]path parametr did not exist, and the unix socket path had to be written in the port parameter

The knxconnection element configures the connection to EIBD. The url parameter can be "local:/path/to/unix/socket" if EIBD uses unix sockets or "ip:ip-address:port" if EIBD uses internet sockets (port is optional; default = 6720)

The exceptiondays element contains a list of days requiring special behavior. The timer conditions in the rules section can be configured to ignore exceptiondays or to evaluate as true or false in case of exceptiondays. Missing day, month or year parameter in a date element is considered as wildcard.

The ioports element contains a list of IO ports. See [IO_Ports] for more details.

The location element defines physical location of KNX installation (latitude (lon) and longitude (lat) in degrees, values have to be negative for south and western hemisphere respectively). This is needed for calculation of sunrise, sunset and noon time specification for conditions etc.

The persistence element defines where linknx stores its persistent status information and/or logs. Persistence type="file" stores values on the filesystem. The path attribute tells where to store persistent status and the logpath attribute tells where to store objects' history logs. By default, path equals /var/lib/linknx/persist and logpath equals path.

Under this directory, a file named the same as the object's id is created for each persistent object (object with attribute init="persist"). This file contains the object's current value.

Under the logpath directory, a file with the object id as name and extension ".log" will be created for each logged object (object with attribute log="true"). This file contains the history of the object in format "yyyy-mm-dd hh:mm:ss > value".

```xml
<persistence type="file" path="/var/lib/linknx/persist/" logpath="/var/lib/linknx/logs/" />
```

Since release 0.0.1.23, persistence type="mysql" is also supported (experimental).
Details in [Mysql_persistence] page.
