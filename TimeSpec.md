THIS PAGE WAS IMPORTED FROM SOURCEFORGE – NEEDS REVIEW!
***

## General

The type of timespec is determined by the _**type**_ attribute.  
Supported values for this attribute are: _**constant**_ and _**variable**_ (default is _**const**_). Since version 0.0.1.25, _**sunrise**_, _**sunset**_ and _**noon**_ are also available. 

## Constant:

Optional attributes _**year**_, _**month**_, **day**, _**hour**_ and _**min**_ are numbers and define together one or more points in time matching the timespec. The default value is -1 and is considered as a wildcard value.  
Optional attribute _**wdays**_ is a string containing the days of the week where the timespec can match. 1 is monday and 7 is sunday (e.g. "67" means the timespec will match only during weekend days). Default value is "1234567".  
Optional attribute _**exception**_ tells if the timespec must match taking into account the exception days defined in _**services**_ section of configuration.  
Value true or yes means the timespec will match only during exception days.  
Value false or no means the timespec will not match during exception days.  
If _**exception**_ attribute is not specified, the default value is dont-care and means  
that the exception days list is not taken into account.  
Examples:  
&lt;at hour="7" min="30" wdays="12345" exception="no" /&gt; will match at 7:30 AM every weekday except days in the exception days list.  
&lt;at hour="10" min="0" wdays="12345" exception="yes" /&gt; will match at 10:00 AM every weekday that is also in the exception days list.  
&lt;at hour="7" min="30" wdays="12345" /&gt; will match at 7:30 AM every weekday whatever is in the exception days list. 

## Variable:

Similar to _**constant**_ timespec, but with additional optional _**time**_ and _**date**_ attributes referring to objects of type EIS3 and EIS4 respectively. For every wildcard in the timespec, the value will be obtained from the correcponding object.  
Examples:  
&lt;at type="variable" time="wakeup_time" wdays="12345"&gt; will match at the time stored in object "wakeup_time" every weekday.  
&lt;at type="variable" hour="22" min="00" date="alarm_date"&gt; will match at 10:00 PM on the day stored in "alarm_date". 

## Sunrise, sunset and noon:

These timespec types are only available since version 0.0.1.25.  
This needs a new entry in &lt;services&gt; section of configuration file to specify the geographic coordinates (longitude and latitude). Example:  
&lt;location lon="-4.20" lat="50.54"/&gt;

In &lt;at .../&gt; or &lt;until .../&gt; elements, you can set _**type**_ attribute to _**sunrise**_ , _**sunset**_ or _**noon**_. Of course, using _**hour**_ and _**min**_ attributes is useless, but _**day**_, _**month**_ and _**year**_ still works, even if in most case you will use _wdays_ and/or _**exception**_ instead. The _**offset**_ is a number of seconds added to the computed sunrise/set time. (negative offset should be possible also). A number followed by letter "d", "h", "m" or "s" will be interpreted as a number of days, hours, minutes or seconds. 

Here is an example of condition that becomes true on modays and tuesdays 2 hours before sunrise and becomes false the same days at 23:58.&nbsp;: 

&lt;condition type='timer' trigger='true'&gt;  
&lt;at type='sunrise' wdays='12' offset="-2h"/&gt;  
&lt;until hour='23' min='58' wdays='12'/&gt;  
&lt;/condition&gt;
