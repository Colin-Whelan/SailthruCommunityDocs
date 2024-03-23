# Logging promocodes with append_user_var()  and using {view_url}
Logging promocodes to profiles is often needed for followup emails and for support. In such cases, the only way to log the data is with `append_user_var()` which works great in emails. The `view_url` complicates things.

In the 'view online' version, the entire template is reprocessed on each new page load. This also reprocesses the `append_user_var()` code and resaves values to the profile. At the same time, promocodes show 'sample_code' instead of the raw code on the 'view online' version. This combo means that on the 'view online' version with promocodes, 'sample_code' would be saved to the profile, overwriting the correct code.

**Solution:** The fix is quite simple.

Instead of this:
``` handlebars
{code = promotion().code}

{append_user_var("myPromoCode", code, 1)}
``` 

Do this: 
``` handlebars
{code = promotion().code}

{* New Check - Use what's in the profile if code showing default *}
{if code == "sample_code" && length(myPromoCode) > 0}
{code = myPromoCode[0]}
{/if}

{append_user_var("myPromoCode", code, 1)}
``` 

The new code simply checks whether the code is 'sample_code' or not and replaces it with what's saved to the profile. In the email the code will display as normal.

This setup has the added benefit of allowing codes to show in the Triggered Send Log view as well as the Builder previews - provided the previewed person has codes assigned to the profile already.