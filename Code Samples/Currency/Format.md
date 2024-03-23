# Format Currency
``` handlebars
{* add $ to a float number and also handling signs *}
{formatCurrency = lambda x: (x < 0 ? "-$" : "$") + (x < 1 && x > -1 ? "0" : "") + number(abs(x),2)}
```