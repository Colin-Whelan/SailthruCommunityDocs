# Some Images not loading with Content Library
Images referenced with 'http://' won't show in image preview, but 'https://' images will show as expected. This only affects the Preview, the final emails are unaffected.

**Solution:** Use 'https' in all image references, or add where applicable:
``` handlebars
{myImageUrl = replace(myImageUrl, "http://", "https://")}
```