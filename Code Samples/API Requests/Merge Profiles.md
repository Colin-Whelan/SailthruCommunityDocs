# Merge Profiles by Email Address
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