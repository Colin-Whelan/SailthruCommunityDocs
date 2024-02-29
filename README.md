
# Welcome to the Sailthru Community Docs

Welcome to Sailthru Community Docs – your resource for optimizing email automation and overcoming common challenges in Sailthru. Whether you're a seasoned email developer or just starting out, this is designed to enhance your knowledge, share best practices, and foster collaboration.

**Help Make Sailthru Community  Docs Awesome!**

Got some cool ideas or fixes up your sleeve? We'd love for you to toss them into the mix! Here's how you can make your mark:

1.  **Got Ideas? Post 'em in Issues:** If you've got a nifty feature idea or spotted something that could be better, head over to the Issues and submit an idea. Don't be shy, every idea helps us grow!
    
2.  **Code Wizards, Your Pull Requests Are Welcome:** If you've cracked a bug or brewed up some code magic, we're all ears (and eyes!). Shoot us a pull request to update directly. 
    
3.  **Join the Banter in the Discussions Tab:** Have questions, tips, or just want to chat with fellow Sailthru buffs? Jump into the Discussions tab. It's all about sharing, learning, and connecting.

# Table of Contents
- [Bugs/Quirks](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#bugsquirks)
	- [Code](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#code)
	- [Lifecycle Optimizer (LOs)](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#lifecycle-optimizer-los)
	- [Triggered Send Log](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#triggered-send-log)
	- [Hosted Pages (Visual Editor)](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#code-samples)
- [Code Samples](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#code-samples)
	- [API Requests](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#api-requests)
	- [Content Library](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#content-library)
- [Tips](https://github.com/Colin-Whelan/SailthruCommunityDocs/tree/main?tab=readme-ov-file#tips)

# Bugs/Quirks
## Code
### assert()
It's not [explicitly documented](https://getstarted.sailthru.com/developers/zephyr-functions-library/assert/), but assert() **DOES NOT** stop LO Flows from running. Further steps will still execute.

[See here for adding a custom Event Log with assert()/cancel()](https://github.com/Colin-Whelan/SailthruCommunityDocs/blob/main/README.md#event-log-for-messages-canceled-with-assert--cancel).

### round()
The [documentation](https://getstarted.sailthru.com/developers/zephyr-functions-library/round/) is incorrect - it will not display as a raw whole number, it will display as a number with a single decimal point.
``` handlebars
{round(50.5)}  = 51.0
``` 
To display as an int like the documentation, use int() as well:
``` handlebars
{int(round(50.5))}  = 51
``` 

### split()
[Existing Docs](https://getstarted.sailthru.com/developers/zephyr-functions-library/split/) make no mention of limitations or odd behavior. 

So you may expect this: 
``` handlebars
{test = "Some Custom > Text"} 
{split(test, " > ")}
``` 

To produce this: 
``` handlebars
["Some Custom", "Text"]
```

But instead you get this: 
``` handlebars
[Some, Custom, Text]
```

The delimiter '*string*' is being treated as an array of single character delimiters, where each character will split the string. This goes against all modern programming languages and makes no sense.

The documentation also shows that the resulting array should have double quotes around each string, yet there are none.

**Solution:** No native solution

**Workaround:**  If possible, use replace() first to remove extra characters, then use split() with a single character only.
``` handlebars
{test = "Some Custom > Text"} 
{test = replace(test, " > ", ">")}
{split(test, ">")}
= 
[Some Custom, Text]
``` 

## Hosted Pages (Visual Editor) 

Middle clicking/right click + open in new tab, does not work for pages made with the visual editor. The link on these hosted visual pages is missing a part of the link.  

URLs look like this: href="/hosted_page_composer/{id}"  
when it should be: href="/content/hosted_page_composer/{id}"

**Solution:** No native solution

**Workaround:** [See this comment for custom script fix](https://sailthru.zendesk.com/hc/en-us/community/posts/18554081415444/comments/19881700113684) 

## HTML Editor
On Firefox only, scrolling jumps way to many lines (~150 lines)

**Solution:** [Feature Request for solution:](https://sailthru.zendesk.com/hc/en-us/community/posts/19528756057748-Improve-HTML-Builder) 

**Workaround:** [See this comment for custom script fix](https://sailthru.zendesk.com/hc/en-us/community/posts/19528756057748/comments/19529068525588) 

## Profiles
### Locked Hardbounce Status
Once a user is marked as 'hardbounce' there is no natural/automatic way to remove this status. This may be needed if for example a customer hardbounced then changed their email. The act of changing the email to something valid indicates they still wish to receive emails. This can happen for reasons such as a typo or a customized email that stopped being used.

**Solution:** Use the UI to change the status to 'Valid'. **Make sure to preserve the opt-out status.** This is not tracked in any system, it's recommended changes be tracked internally.

## Lifecycle Optimizer (LOs)

### 'Ghosts' in LOs
After removing 'Wait' actions, users from that step will still wait their original wait period before moving on/exiting.
During this wait period they are invisible in the UI and API. 

**Solution:** The only solution is to set the LO to 'inactive' which fully removes everyone from the LO, then switch back to 'active'. 

## Template Previews
### Some Images not loading with Content Library
Images referenced with 'http://' won't show in image preview, but 'https://' images will show as expected. This only affects the Preview, the final emails are unaffected.

**Solution:** Use 'https' in all image references, or add where applicable:
``` handlebars
{myImageUrl = replace(myImageUrl, "http://", "https://")}
```


## Triggered Send Log
### Stuck Selection
Occasionally the selection will get 'stuck'. Selecting other templates *appears* to update the results, but they still show for original selection.

**Solution:** Reload the page and select again.

# Code Samples

## API Requests
Used either with the API clients, or [with the web UI](https://my.sailthru.com/api/test).

### Custom Events
Sends API events to initiate LOs and triggers.

Uses: POST - event
vars are optional and can be completely omitted if not used.
``` handlebars
{
  "id" : "email_address",
  "event"  : "event_name",
  "vars" : {
    "url" : "http://example.com/123",
    "name" : "Widget"
  }
}
```

### Lookup Profile by extID
This request returns the profile data for the customer with that ID. The email/SID can be used with Native search to show the normal profile view.

Uses: GET- user
``` handlebars
{
  "id":"extID_to_lookup",
  "key":"extid"
}
```

### Merge Profiles by Email Address
Other keys can be used, but email is simplest and easiest to use.

This works whether the old email or the new one has the extid. In either case the '**new_email_address**' will be the email of the account, the extid will be assigned, and the other profile is deleted. If the old profile was a hardbounce, it will remain to prevent future sends.

Uses: POST - user
``` handlebars
{
    "id":"old_email_address",
    "key": "email",
    "keys":
        {
            "email":"new_email_address"
        },
    "keysconflict":"merge"
}
```
Success response: 
``` handlebars
{
    "ok" : true
}
```

## Content Library
### Lookup Product (SKU)
``` handlebars
{* Searches for sku match, returns first result. Uses 'replace' to remove any spaces *}
{response = personalize({"algorithm": "custom", "custom_keys": [replace(sku, " ", "")], "custom_key_type": "sku","include_vars":true})}
{skuLookupResponse  = response[0]}
```
**!! WARNING !!**
There is a hidden property from the UI called 'expire_date' which will prevent some products/articles from being returned unless this property is added: `"allow_expired":true`
This hidden value shows when the product will expire, but the only way to view the field is after this special parameter is added.

## Currency
### Format Currency
``` handlebars
{* add $ to a float number and also handling signs *}
{formatCurrency = lambda x: (x < 0 ? "-$" : "$") + (x < 1 && x > -1 ? "0" : "") + number(abs(x),2)}
```

### Format Currency (French)
``` handlebars
{* add $ to a float number and also handling signs *}
{formatCurrencyFr = lambda x: (x < 0 ? "-" : "") + (x < 1 && x > -1 ? "0" : "") + replace(replace(number(abs(x),2), ","," "),".",",") + " $"}
```

## Dates
### Show date in bilingual format (EN + FR) 
Required variables:
`input_date` = your date(supported formats: All main formats. If using '/' must be MM/DD/YY. Use YYYY if <2000
`language` = the current language as a 2-letter string

``` handlebars
{input_date = replace(input_date, "T", " ")}

{* Initializes date-related variables and formats a given date into a list with various date elements *}
{french_months = ["month", "janvier", "février", "mars", "avril", "mai", "juin", "juillet", "août", "septembre", "octobre", "novembre", "décembre"]}
{date_components = [input_date, time(input_date), int(date("yyyy", time(input_date))), int(date("M", time(input_date))), int(date("d", time(input_date))), (int(date("d", time(input_date))) == 1 ? int(date("d", time(input_date))) + "er" : int(date("d", time(input_date))))]}
{initializeDateFormatting = lambda input_date: map([french_months, date_components], lambda x: x)}

{* Displays the date in a specified language format, French or default (English) *}
{displayFormattedDate = lambda language: language == "fr" ? (date_components[5] + "&nbsp;" + french_months[date_components[3]] + "&nbsp;" + date_components[2]) : (date("MMMM", date_components[1]) + "&nbsp;" + date("d, yyyy", date_components[1]) )}
```

**Example** 
``` handlebars
{input_date = "2023-11-01T19:00:34.733Z"}
{language = "fr"}

Formatted date: {displayFormattedDate(language)}
```
Gives:
Formatted date: 1er novembre 2023

## Event Log for Messages Canceled with assert() + cancel()
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


# Tips
## Consent Management
The default optout page allows recipients to unsubscribe from individual templates. There is no known way to view which template(s) a profile is unsubscribed from.

It's also not clear how to resubscribe/remove this status other than having the recipient click the original link and clicking 'Opt back in' button on the landing page. Resending the template is not possible since they have been unsubscribed to it, and using a new template will mean it's no longer connected to the original template they unsubbed from and will only allow them to unsub from that template.

Changing the template name often doesn't work because it's using the Public Name to track subscriptions, which is only available to edit within HTML templates, not Visual templates. It is possible to edit this value with a tool like [Sailaway](https://github.com/Colin-Whelan/SailAway) to modify the contents of the corresponding options file with a new name and pushing the update.

When getting started with this, it's recommended that you change the logic of the optout page to only allow them to unsub from all emails and/or from certain lists, rather than individual templates.

## Promotion Codes in Campaigns

### Campaigns End When Promotion Codes Are Empty
If promotion codes run out mid-way through a Campaign, the Campaign will stop at that point and end the Campaign. This is a nice safety feature, but isn't documented anywhere.

## Triggered Send Log

### Template Ordering
Templates starting with lowercase letters are ordered last. With 100s of templates this isn't always immediately obvious.

Uses this sorting:
01234567890ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz


### No 'time filter' support
While there isn't an easy way to input time, it IS supported. 

[See this feature request for more info](https://sailthru.zendesk.com/hc/en-us/community/posts/19300998622612-Improved-Triggered-Send-Log) 
