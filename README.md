

# Welcome to the Sailthru Community Docs

Welcome to Sailthru Community Docs – your resource for optimizing email automation and overcoming common challenges in Sailthru. Whether you're a seasoned email developer or just starting out, this is designed to enhance your knowledge, share best practices, and foster collaboration.

**Help Make Sailthru Community  Docs Awesome!**

Got some cool ideas or fixes up your sleeve? We'd love for you to toss them into the mix! Here's how you can make your mark:

1.  **Got Ideas? Post 'em in Issues:** If you've got a nifty feature idea or spotted something that could be better, head over to the Issues and submit an idea. Don't be shy, every idea helps us grow!
    
2.  **Code Wizards, Your Pull Requests Are Welcome:** If you've cracked a bug or brewed up some code magic, we're all ears (and eyes!). Shoot us a pull request to update directly. 
    
3.  **Join the Banter in the Discussions Tab:** Have questions, tips, or just want to chat with fellow Sailthru buffs? Jump into the Discussions tab. It's all about sharing, learning, and connecting.

# Bugs/Quirks
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

## Lifecycle Optimizer (LOs)
### Locked Hardbounce Status
Once a user is marked as 'hardbounce' there is no natural/automatic way to remove this status. This may be needed if for example a customer hardbounced then changed their email. The act of changing the email to something valid indicates they still wish to receive emails. This can happen for reasons such as a typo or a customized email that stopped being used.

**Solution:** Use the UI to change the status to 'Valid'. **Make sure to preserve the opt-out status.** This is not tracked in any system, it's recommended changes be tracked internally.

### 'Ghosts' in LOs
After removing 'Wait' actions, users from that step will still wait their original wait period before moving on/exiting.
During this wait period they are invisible in the UI and API. 

**Solution:** The only solution is to set the LO to 'inactive' which fully removes everyone from the LO, then switch back to 'active'. 

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

# Tips
## Triggered Send Log

### Template Ordering
Templates starting with lowercase letters are ordered last. With 100s of templates this isn't always immediately obvious.

Uses this sorting:
01234567890ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz


### No 'time filter' support
While there isn't an easy way to input time, it IS supported. 

[See this feature request for more info](https://sailthru.zendesk.com/hc/en-us/community/posts/19300998622612-Improved-Triggered-Send-Log) 
