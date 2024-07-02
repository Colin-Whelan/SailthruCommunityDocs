# round()
The [round() function](https://getstarted.sailthru.com/developers/zephyr-functions-library/round/) behaves differently from standard implementations in many programming languages. It returns a value with one decimal place, rather than a raw integer.

``` handlebars
{round(50.5)}  = 51.0
``` 
To obtain an integer result as commonly expected, combine `round()` with the `int()` function:

``` handlebars
{int(round(50.5))}  = 51
``` 
