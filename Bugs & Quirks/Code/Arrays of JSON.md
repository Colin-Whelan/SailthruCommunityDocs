# Working with Arrays of JSON in Zephyr Templates

When dealing with complex data structures in Sailthru Zephyr templates, you'll often need to work with arrays of JSON objects. 
This guide covers the an improved format for defining these arrays in a way that is easy to read and maintain.

## Improved Syntax for Multi-line JSON Arrays

Zephyr requires a specific formatting approach for multi-line JSON arrays to be parsed correctly. 
The opening brace of each object needs to be on the line before its content.

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

### Incorrect Format (Will Not Work):
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
