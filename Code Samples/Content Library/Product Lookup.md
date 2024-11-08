# Lookup Product (SKU)
``` handlebars
{* Searches for sku match, returns first result. Uses 'replace' to remove any spaces *}
{response = personalize({"algorithm": "custom", "custom_keys": [replace(sku, " ", "")], "custom_key_type": "sku","include_vars":true})}
{skuLookupResponse  = response[0]}
```

**!! WARNING !!**

There is a property in the UI called 'expire_date' which will prevent some products/articles from being returned unless this property is added: `"allow_expired":true`
This hidden value shows when the product will expire and is very easy to miss. Once missed it's difficult to diagnose.
