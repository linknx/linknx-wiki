This page describes the syntax and the various scenarios supported by the rules section of a linknx [configuration](Configuration) file.

A rule consists of:
- one [condition](Conditions) with at least one trigger defined,
- an optional [action list](Actions) to be executed when the condition evaluates to true (type="**if-true**"),
- another optional action list to be executed when the condition evaluates to false (type="**if-false**"),
- another optional action list to be executed when the condition just evaluates to true while its previous value was false (i.e. the condition just turned true) (type="**on-true**"),
- another optional action list to be executed when the condition just evaluates to false while its previous value was true (i.e. the condition just turned false) (type="**on-false**"),

```xml
<rule id="xxxx">
	<condition type="..." trigger="true">
		<!--...-->
	</condition>
	<actionlist>
		<action ... />
		<!--...-->
	</actionlist>
	<actionlist type="on-false">
		<action ... />
	</actionlist>
	<actionlist type="if-true">
		<action ... />
	</actionlist>
	<actionlist type="if-false">
		<action ... />
	</actionlist>
</rule>
```

The rule is evaluated whenever one of its conditions with *trigger="true"* sees its value change. Linknx decides then which actionlist should be executed, depending on the previous and update value of the condition.

You can read a more in-depth description of actions and conditions in their respective sections. But in order to help impatient people get started, let's examine a few sample rules.

## Sample #1: a scheduled action repeated on a regular basis

This first example shows a rule which purpose is to update every 3600 seconds (every hour, then) *cur_time* and *cur_date* objects with the current date & time.

```
<rule id="cur_time_date">
    <condition type="timer" trigger="true">
         <every>3600</every>
    </condition>
    <actionlist>
          <action type="set-value" id="cur_time" value="now" />
          <action type="set-value" id="cur_date" value="now" />
    </actionlist>
</rule>
```

## Sample #2: a heating system controller

```xml
<rule id="heating_morning">
	<condition type="and">
		<condition type="object" id="heating_auto" value="on" />
		<condition type="or">
			<condition type="timer" trigger="true">
				<at hour="6" min="30" exception="no" wdays="12345" />
				<until hour="8" min="0" />
			</condition>
			<condition type="timer" trigger="true">
				<at hour="8" min="0" wdays="67" />
				<until hour="12" min="0" />
			</condition>
			<condition type="timer" trigger="true">
				<at hour="8" min="0" exception="yes" />
				<until hour="12" min="0" />
			</condition>
		</condition>
	</condition>
	<actionlist>
		<action type="set-value" id="heating_kitchen" value="comfort" />
		<action type="set-value" id="heating_living" value="standby" />
		<action type="set-value" id="heating_bathroom" value="on" />
		<action type="set-value" id="heating_bedroom1" value="comfort" />
		<action type="set-value" id="heating_bedroom2" value="comfort" />
	</actionlist>
	<actionlist type="on-false">
		<action type="set-value" id="heating_kitchen" value="frost" />
		<action type="set-value" id="heating_living" value="frost" />
		<action type="set-value" id="heating_bathroom" value="off" />
		<action type="set-value" id="heating_bedroom1" value="frost" />
		<action type="set-value" id="heating_bedroom2" value="frost" />
	</actionlist>
</rule>
```

When the *heating_auto* object's value is set to *on*, this rule activates the heating at scheduled times. Those actions correspond to the first action list and are scheduled as follows:
- at 6:30 am every week day that is not flagged as an exception day in "services" section
- at 8:00 am every weekend day or day flagged as an exception day

It then deactivates the heating system a few hours later, as per the *until* elements. This deactivation is done by executing the second action list, whose actions are scheduled as follows:
- at 8:00 am every week day that is not flagged as an exception day in "services" section
- at 12:00 am every weekend day or day flagged as an exception day

Note that the object condition on "heating_auto" does not set the *trigger* attribute. As a consequence, the action list is not executed when *heating_auto* is turned on at 7:00 am on a weekday. The condition's value changes but this change does not trigger the rule's execution.

## Sample #3: evaluate a rule at startup:

```xml
<rule id="calculate_any_light_status" init="eval">
	<condition type="or">
		<condition type="object" id="light1" value="on" trigger="true"/>
		<condition type="object" id="light2" value="on" trigger="true"/>
		<condition type="object" id="light3" value="on" trigger="true"/>
	</condition>
	<actionlist>
		<action type="set-value" id="some_light_on" value="on" />
	</actionlist>
	<actionlist type="on-false">
		<action type="set-value" id="some_light_on" value="off" />
	</actionlist>
</rule>
```

The same can be forced to true or false at linknx startup:
- `<rule id="calculate_any_light_status" init="true">`
- `<rule id="calculate_any_light_status" init="false">`

## Sample #4: a *dim-up* action at wake-up time

```xml
<rule id="wakeup_alarm">
	<condition type="and">
		<condition type="object" id="absence" value="off" />
		<condition type="object" id="wakeup_active" value="on" />
		<condition type="timer" trigger="true">
			<at hour="6" min="30" exception="no" wdays="12345" />
		</condition>
	</condition>
	<actionlist>
		<action type="dim-up" id="dim_value_bedroom2"
			start="0" stop="240" duration="1800" />
	</actionlist>
</rule>
```

This rule dims the bedroom light up smoothly from 0 to 94% (=240/255) during half an hour (=1800 sec) at 6:30 am on every weekday which is an exception day. Note that this feature is enabled when two criteria are met:
- the *wakeup_active* object is set to *on*
- people are at home (object *absence* is set to *off*)

# Rules on linknx startup

Please note, that some rules are evaluated and triggered when linknx starts up and reads the configuration file while some are not. This can be to some extent controlled by the init=true or init=false attribute. The detailed [Linknx_rules_on_startup] page contains detailed discussion of this topic. 