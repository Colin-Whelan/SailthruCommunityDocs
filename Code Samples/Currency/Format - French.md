# Format Currency (French)
``` handlebars
{* add $ to a float number and also handling signs *}
{formatCurrencyFr = lambda x: (x < 0 ? "-" : "") + (x < 1 && x > -1 ? "0" : "") + replace(replace(number(abs(x),2), ","," "),".",",") + " $"}
```