# Consent Management
The default optout page allows recipients to unsubscribe from individual templates. There is no known way to view which template(s) a profile is unsubscribed from.

It's also not clear how to resubscribe/remove this status other than having the recipient click the original link and clicking 'Opt back in' button on the landing page. Resending the template is not possible since they have been unsubscribed to it, and using a new template will mean it's no longer connected to the original template they unsubbed from and will only allow them to unsub from that template.

Changing the template name often doesn't work because it's using the Public Name to track subscriptions, which is only available to edit within HTML templates, not Visual templates. It is possible to edit this value with a tool like [Sailaway](https://github.com/Colin-Whelan/SailAway) to modify the contents of the corresponding options file with a new name and pushing the update.

When getting started with this, it's recommended that you change the logic of the optout page to only allow them to unsub from all emails and/or from certain lists, rather than individual templates.

