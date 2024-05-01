# Event Log for Messages Cancelled with assert() + cancel()
If using assert() + cancel(), it is really beneficial to log the failures. This can be done with the use of [Template Triggers](https://getstarted.sailthru.com/email/trig-transac/create-template-triggers/) - only works with HTML Templates. 

In the HTML template, go to 'Triggers' (the last tab, might have to have this enabled on your space), add a Trigger for the 'cancel' event with a time of 0 minutes and use the 'Custom Zephyr Script' Action with this as the code:
``` handlebars
{thisEvent = {"event":"X Template Failure","date":date("E, d MMM yyyy HH:mm:ss Z", time("now")),"cartSkus":cartSkus,"failureMessage":event.failure_message}}

{logLimit = 50}
{eventLogs = profile.vars.eventLog_Failures}

{*If the existing log is at or past the logLimit(50), trim the oldest entries and add the new event*}
{if length(eventLogs) >= logLimit }
{eventLogs = slice(eventLogs, length(eventLogs) - (logLimit - 1))}
{else if !eventLogs}
{eventLogs = []}
{/if}

{push("eventLogs", thisEvent)}

{api_user({"vars": {"eventLog_Failures": eventLogs}})}
```

This will capture any failures and log the data to the customers profile. Custom data can be added as needed.  Other templates can use the same code and share the `eventLog_Failures` field.

## Capturing Profile and Event Data
During the 'cancel' trigger, the normal access to event and profile data is restricted, with the exception of 'event' which contains details about the assert() condition that failed.

The only section that can be modified is the failure message. Normally this is a simple string about the condition, but in this special case you can make use of it to get access to more complex data like Profile data or parts of the LO entry event data to the profile as seen in this test:
``` handlebars
{* Takes event from payload + random value from 0 - 10 *}
{failureMessage = {"testField": value.from.payload,"testField2": replace(round(random() * 10),".0","")}}
{assert(false, failureMessage)}
```

With this Trigger: cancel - 0 minutes - Custom Zephyr Script:
``` handlebars
{api_user({"vars": {"Test_Log": event.failure_message}})}
```
Results in this data:

| Custom Field Name | Value | Type |
| ------------- | ------------- | ------------- |
| Test_Log | 	{"testField2":"2","testField":"from the payload!"} | String |

Note that the _type_ is still String. There is currently no known way to have this treated as a object. This may limit the use if the value is required for other tempates or LOs, but may still be helpful for event logging.
