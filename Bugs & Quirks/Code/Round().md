# round()
The [documentation](https://getstarted.sailthru.com/developers/zephyr-functions-library/round/) is incorrect - it will not display as a raw whole number, it will display as a number with a single decimal point.

``` handlebars
{round(50.5)}  = 51.0
``` 

To display as an int like the documentation, use int() as well:

``` handlebars
{int(round(50.5))}  = 51
``` 