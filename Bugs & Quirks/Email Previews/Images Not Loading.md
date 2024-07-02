# Some Images not loading with Content Library
Images referenced with 'http://' don't always show in previews, while 'https://' images will show as expected. This only affects the Preview, the final emails are unaffected.

**Solution:** Use 'https' in all image references, or add where applicable:
``` handlebars
{myImageUrl = replace(myImageUrl, "http://", "https://")}
```
