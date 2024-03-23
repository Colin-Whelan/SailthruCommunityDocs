# Show date in bilingual format (EN + FR) 
Required variables:
`input_date` = your date(supported formats: All main formats. If using '/' must be MM/DD/YY. Use YYYY if <2000)
`language` = the current language as a 2-letter string

``` handlebars
{input_date = replace(input_date, "T", " ")}

{* Initializes date-related variables and formats a given date into a list with various date elements *}
{french_months = ["month", "janvier", "février", "mars", "avril", "mai", "juin", "juillet", "août", "septembre", "octobre", "novembre", "décembre"]}
{date_components = [input_date, time(input_date), int(date("yyyy", time(input_date))), int(date("M", time(input_date))), int(date("d", time(input_date))), (int(date("d", time(input_date))) == 1 ? int(date("d", time(input_date))) + "er" : int(date("d", time(input_date))))]}
{initializeDateFormatting = lambda input_date: map([french_months, date_components], lambda x: x)}

{* Displays the date in a specified language format, French or default (English) *}
{displayFormattedDate = lambda language: language == "fr" ? (date_components[5] + "&nbsp;" + french_months[date_components[3]] + "&nbsp;" + date_components[2]) : (date("MMMM", date_components[1]) + "&nbsp;" + date("d, yyyy", date_components[1]) )}
```

**Example** 
``` handlebars
{input_date = "2023-11-01T19:00:34.733Z"}
{language = "fr"}

Formatted date: {displayFormattedDate(language)}
```

Gives:

Formatted date: 

1er novembre 2023