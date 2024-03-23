# split()
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

The delimiter '*string*' is being treated as an array of single character delimiters, where each character will split the string. This makes some sense as strings are arrays of characters, but it's not what you'd expect.

The documentation also shows that the resulting array should have double quotes around each string, yet there are none when displaying in the preview like you might expect.

**Solution:** No native solution.

**Workaround:**  If possible, use replace() first to remove extra characters, then use split() with a single character only.
``` handlebars
{test = "Some Custom > Text"} 
{test = replace(test, " > ", ">")}
{split(test, ">")}
= 
[Some Custom, Text]
``` 