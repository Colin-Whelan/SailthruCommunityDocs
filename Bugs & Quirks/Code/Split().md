# split()
The [split() function](https://getstarted.sailthru.com/developers/zephyr-functions-library/split/) exhibits behavior that may not align with common expectations or implementations in other programming languages. While the official documentation does not explicitly mention these nuances, they are important to consider when using this function.

Expected behavior:
``` handlebars
{test = "Some Custom > Text"} 
{split(test, " > ")}

Expected output: ["Some Custom", "Text"]
``` 

Actual behavior: 
``` handlebars
{test = "Some Custom > Text"} 
{split(test, " > ")}

Actual output: [Some, Custom, Text]
```
The function interprets the delimiter string as an array of single-character delimiters, where each character independently splits the string. While this behavior aligns with the concept of strings as character arrays, it may not match typical expectations for string splitting operations.

Additionally, the documentation suggests that the resulting array elements should be enclosed in double quotes. However, the preview display omits these quotes. This has no effect actual use. They are in fact strings.

To approximate the desired result, consider using `replace()` to simplify the delimiter before applying `split()` with a single character:

``` handlebars
{test = "Some Custom > Text"} 
{test = replace(test, " > ", ">")}
{split(test, ">")}

Output: [Some Custom, Text]
``` 
