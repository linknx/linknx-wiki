# General

The type of condition is determined by the `type` attribute.  
Supported values for this attribute are:  `and`, `or`, `not`, `object`, `object-compare`, `object-src`, `timer`, `time-counter`, `ioport-rx` and `script`

# Logical Conditions: `and`, `or`, `not`

The only attribute for these is `type`. Conditions of type `and` or `or` can have one or several children conditions, while condition of type `not` can only have one.
As expected, their behavior is as follows:
- `and` conditions evaluate to `true` if all child conditions evaluate to `true`.
- `or` conditions evaluate to `true` if at least one child condition evaluates to `true`.
- `not` conditions negates their child condition. As a consequence, they evaluate to `true` if and only if the child condition evaluates to `false`.

# Timer Conditions

The only attribute – optional – for this type of condition is `trigger`. Its value is either `true` or `false` and it defaults to `false`.
Setting the `trigger` attribute to `true` forces the rule to assess its top-level condition whenever the value of the timer condition changes. In other words, with `trigger` set to true, the status of the rule may change immediately upon a change of the timer condition. On the contrary, setting it to `false` does not force the rule to refresh its status after the sole change of the timer condition.
  
This type of condition is defined by means of its children elements. They can be:
- `at`
- `every`
- `until`
- `during`

Elements `at` and `every` are mutually exclusive. One of the two **must** be present for the condition to be valid.
Elements `until` and `during` are mutually exclusive. Their presence is optional.
For details about attributes for `at` and `until`, please refer to section about the [time specification](TimeSpec) section.
Elements `every` and `during` cannot have attributes. Their internal text value defines a period of time in seconds.  
At the time specified by `at` or time interval specified by `every` , the timer condition evaluates to `true`.
When the time specified by `until` is reached or when the time duration specified by `during` is expired, the timer condition evaluates to `false`.
If neither `until` nor `during` are specified, the timer condition expires instantaneously. That means that when scheduling constraints are met, the timer condition briefly evaluates to `true`, causing the rule to re-evaluate if `trigger` is `true` and immediately resets to `false`.
Please note that you can force the timer condition to remain `false` on exception days, by setting `exception="no"`. Exception days requiring special treatment are defined in the [services](Services) section.

## Comments

* Please note that you can use a combination of timer conditions where some are used to trigger evaluation at a given time while others are define the general timeframe when a condition can be valid.
The example below illustrates this feature with a condition that should trigger every day at 8:00 AM in the period ranging from mid-September to mid-March.
```xml
<condition type="and">
    <condition type="timer">
        <at day="15" month="9" hour="0" min="0"/>
        <until day="15" month="3" hour="0" min="0"/>
    </condition>
    <condition type="timer" trigger="true">
        <at hour="8" min="0"/>
    </condition>
</condition>
```

* Any parts of the time specification can be omitted. For example, the condition below evaluates to `true` every day of February because it does not define any constraint on the day of month: 

```xml        
<condition type="timer" trigger="true">
    <at hour="8" min="12" month="2" exception="no"/>
</condition>
```

# Object Conditions

Like [timer conditions](#Timer-Conditions), object conditions have an optional `trigger` boolean attribute which defaults to `false`.

TO BE CONTINUED, read further on [the sourceforge wiki](https://sourceforge.net/p/linknx/wiki/Condition%27s_Syntax/).