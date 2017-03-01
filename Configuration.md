# Overview
Looking at the XML configuration file (example here), we can see 4 main sections: services, logging, objects and rules. Services contains all the system wide parameters like ip-address/port of EIBD, sms or email server parameters. Logging contains the logging configuration. Objects contains the description of all group objects. Rules contains a list of rules with a condition and list of actions that are executed based on this condition.

```xml
<config>
    <objects>
        ....
    </objects>
    <rules>
        ....
    </rules>
    <services>
        ....
    </services>
    <logging .... />
</config>
```

The four sections can be in any order, some of them are optional, but if a section appears more than once, only the first occurrence of that section will be processed.

# Services section

This section is responsible for defining most options (like exception days, type of persistence files, ip and port where XML interface is presented to the outside world), and for configuring external services consumed by the server: connection to KNX bus backend, I/O ports, SMS and email gateways, etc.

Here is an example of the _services_ section

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

For a detailed definition of the services and their syntax please refer to the [Services section page](Services-Section).

# Logging section

This section is responsible for controlling the creation and format of log files. Linknx support 2 different logging back-ends:

- Internal logger
- log4cpp external library

A sample logging section could look like this:

```xml
<logging config="/var/lib/linknx/logging.conf"/>
``` 

Detailed syntax is explained in [Logging_section] page.
Objects definition section

This is the main part of the linknx configuration. All KNX objects that linknx interacts with (in the rules) and which can be accessed by the web visualisation [KnxWeb] are defined here.

Here is an example of objects definition section:

&lt;objects&gt;
      &lt;object type="16.000" id="text_salon" gad="9/1/153" flags="cwtu"&gt;Text on LCD in salon&lt;/object&gt;
      &lt;/object&gt;
      &lt;object id="hall_lamps_central" gad="3/2/1" init="request"&gt;Hall central lamp&lt;/object&gt;
      &lt;object id="hall_lamps_LED" gad="3/2/2" init="request"&gt;Hall LED floor lights&lt;/object&gt;
      &lt;object id="gate_open" gad="0/1/0" init="request"&gt;&lt;listener gad="4/1/0"/&gt;gate open close&lt;/object&gt;
      &lt;object id="gate_status" gad="4/1/0" init="request"&gt;gate state status&lt;/object&gt;
      &lt;object id="salon_scene1" gad="3/2/2" init="persist"&gt;Salon teleconference start scene&lt;/object&gt;
&lt;/objects&gt;

Since version 0.0.1.25, the object definition uses Datapoint Types defined in KNX standard instead of EIS datatypes.
The current syntax is described in [Object_Definition_Syntax] section.
The deprecated syntax (before 0.0.1.25) is still supported but will be converted to the new one when loaded by the application.
The old syntax is described in [Object_Definition_Syntax_before_0.0.1.25] section.
Rules section

Rules section is a place where building automatisation is created. You can define what happens when state of some KNX objects has changed, define actions to be performed during specific time periods etc. It consists of a rules each defining some specific condition and action to be performed when the condition had been met.

A simplest rule is composed of:

    one condition
    an action list to be executed when the condition is evaluated as true

however more complicated scenarios can be created as described in [Rules_section]

A sample rule:

        &lt;rule id="xxxx"&gt;
            &lt;condition type="...."&gt;
                .....
            &lt;/condition&gt;
            &lt;actionlist&gt;
                &lt;action ..... /&gt;
                .....
            &lt;/actionlist&gt;
            &lt;actionlist type="on-false"&gt;
                &lt;action ..... /&gt;
            &lt;/actionlist&gt;
        &lt;/rule&gt;

The detailed syntax, and a full list of condition and action types is available on the [Rules_section] page. 