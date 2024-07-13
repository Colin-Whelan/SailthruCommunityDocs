# Multi-Line JSON in Zephyr Templates

When dealing with complex data structures in Sailthru Zephyr templates, you'll often want to make use of multi line JSON objects to make readding, editing, and maintaining code easier. 
While multi line JSON is supported, normal formatting will result in errors and instead it relies a non-standard format.

## Improved Syntax for Multi-line JSON Arrays

Zephyr requires a specific formatting approach for multi-line JSON arrays to be parsed correctly. 
The opening brace of each object needs to be on the line before its content.

### Incorrect Format - Will Not Work!:
```handlebars
{articles = [
    {'title': 'Breaking News: Global Summit Announced'},
    {'title': 'New Study Reveals Benefits of Mediterranean Diet'},
    {'title': 'Tech Giant Unveils Revolutionary Smartphone'},
    {'title': 'Record-Breaking Heatwave Sweeps Across Europe'},
    {'title': 'Blockbuster Movie Breaks Box Office Records'},
    {'title': 'Stock Market Soars to All-Time High'8}
]}
```

### Correct Format:
```handlebars
{articles = [{
  'title': 'Breaking News: Global Summit Announced'}, {
  'title': 'New Study Reveals Benefits of Mediterranean Diet'}, {
  'title': 'Tech Giant Unveils Revolutionary Smartphone'}, {
  'title': 'Record-Breaking Heatwave Sweeps Across Europe'}, {
  'title': 'Blockbuster Movie Breaks Box Office Records'}, {
  'title': 'Stock Market Soars to All-Time High'} ]}
```

Putting each bracket pair + key/value pair on their own line also works:
```handlebars
{articles = [{
  'title': 'Breaking News: Global Summit Announced'
  }, {
  'title': 'New Study Reveals Benefits of Mediterranean Diet'
  }, {
  'title': 'Tech Giant Unveils Revolutionary Smartphone'
}]}
```


Which is great if you have more complex JSON:
```handlebars
{articles = [{
  'title': 'Breaking News: Global Summit Announced',
  'category': 'Politics',
  'readTime': 5
}, {
  'title': 'New Study Reveals Benefits of Mediterranean Diet',
  'category': 'Health',
  'readTime': 7
}, {
  'title': 'Tech Giant Unveils Revolutionary Smartphone',
  'category': 'Technology',
  'readTime': 6
}]}
```
