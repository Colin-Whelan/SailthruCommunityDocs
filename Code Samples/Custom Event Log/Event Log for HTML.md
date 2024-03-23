# Event Log for Messages Canceled with assert() + cancel()
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