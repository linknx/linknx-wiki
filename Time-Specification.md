# General

Time specifications are parts of the definition of [conditions](Conditions) of type `timer`. They are an intuitive and versatile way of scheduling a point in time. For example, they can define precise times such as:
- [constant](#Constant) clock time (03:00 PM, 04:15 AM, ...)
- [sunset/sunrise and noon](#solar-time)
- the time [provided by another object](#object-provided-time)

## Common Attributes
Some attributes depend on the way the time is specified but all time specifications can define the following attributes:
- `type` is mandatory and tells how the time is specified. Its value must be one of `const`, `variable`, `sunrise`, `sunset` and `noon`
- `hour`, `min`, `day`, `month` and `year` are optional integers constraining the time specification. If an attribute is undefined or equal to -1, it is interpreted as a wildcard: for example if `hour` is not defined, the hour of day will not be taken into consideration when determining whether the current clock time matches the time specification.
- `wdays` is an optional attribute limiting the scope of the specification to any combination of days of week. Days are identified with their 1-based index in the week, starting with Monday. The value of the attribute is a string featuring one, several or all of those day indices (ex: `125` for Monday, Tuesday and Friday). The default value for this attribute is `1234567`
- `exception` is an optional string telling the behavior of this time specification on days tagged as exceptional in the [services](Services) section of the configuration:
	* `true` or `yes` means the time specification only applies to exception days. It cannot match on a normal day, whatever the time.
	* `false` or `no` means the time specification only applies to normal days. It cannot match on an exception day, whatever the time.
    * Any other value leads to the time specification to operate the same, no matter if the day exceptional or normal. This is the default behavior.
- `offset` is an optional string shifting the matching time backwards or forward by a certain amount of time. It is of the form `{number}{unit}`, `number` being a positive or negative integer and `unit` one of:
	* `s` for seconds (the default)
	* `m` for minutes
	* `h` for hours
	* `d` for days

Behavior on exception days is summarized in the table below:

|                 | `exception="yes"`  |  `exception="no"` | `exception=""` or undefined |
|  :------------: | :----------------: | :---------------: | :-------------------------: |
| Normal day      |     disabled       |   **enabled**     |    **enabled**              | 
| Exceptional day |    **enabled**     |    disabled       |    **enabled**              |

# Type-Specific Configuration

## Constant

A time specification based on a constant time does not require any attributes in addition to those [common to all types](#common-attributes).

### Examples:  

Specification matching all weekdays at 07:30 AM local time except on days flagged as exceptional:
```xml
<at hour="7" min="30" wdays="12345" exception="no"/>
```

Specification matching every exceptional weekday at 10:00 AM:
```xml
 <at hour="10" min="0" wdays="12345" exception="yes"/> 
```

Specification matching all Saturdays and Sundays at 07:30 PM, no matter if they appear in the list of exception days or not:
```xml
<at hour="19" min="30" wdays="67"/>
```

## Object-Provided Time

In addition to the [common attributes](#common-attributes), time specification of type `variable` can define the `time` and `date` attributes. If defined and not equal to the empty string, those attributes must respectively refer to objects of type `10.001` (formerly `EIS3`) and `11.001` (formerly `EIS4`). The time and date read from those objects are used to fill in the gaps of the base definition represented by the common attributes.

Please note that the values in the common attributes always take precedence over constraints provided by the time- and date-providing objects. For example, setting `hour="11" min="0"` would force the time specification to match at 11:00 AM whatever the value of the time-providing object.

### Examples

Specification matching weekdays, at the time stored in the object of identifier `wakeup_time`:
```xml
<at type="variable" time="wakeup_time" wdays="12345"/>
```

Specification matching at 10:00 PM the day corresponding to the date stored in the `alarm_date` object:
```xml
<at type="variable" hour="22" min="00" date="alarm_date"/>
```

## Solar Time

This category of time specification was introduced in version 0.0.1.25 and corresponds to types `sunrise`, `sunset` and `noon`.

For these types to work, the [services](Services) section of the configuration must declare geographic coordinates (longitude and latitude) as in the example below:
```xml
<location lon="-4.20" lat="50.54"/>
```

The specification then targets the time the sun rises, sets or the median time between those two.

As one can expect, defining `hour` or `min` alongside `type=sunrise|sunset|noon` is somehow inconsistent. The solar time will always take precedence over those two basic attributes.
On the other hand, defining `day`, `month` and/or `year` can still supplement the time provided by the solar configuration.

### Example
Here is a sample condition evaluating to true on Mondays and Tuesdays, from two hours before sunrise to 11:58 PM: 

```xml
<condition type="timer" trigger="true">  
	<at type="sunrise" wdays="12" offset="-2h"/>  
	<until hour="23" min="58" wdays="12"/>  
</condition>
```
