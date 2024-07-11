# Contextual Array Enrichment

## Introduction
This advanced cycler pattern enhances template readability and flexibility when working with arrays of items. 
It preprocesses data to add contextual information, making templates more intuitive and easier to maintain.

This advanced cycler pattern preprocesses the array, adding context to each item. 
This context includes information about the item's position (first, last), as well as references to the next and previous items.


## Setup
Given this data:

```handlebars
{articles_m = [{
  'title': 'Breaking News: Global Summit Announced', 'category': 'Politics', 'readTime': 5}, {
  'title': 'New Study Reveals Benefits of Mediterranean Diet', 'category': 'Health', 'readTime': 7}, {
  'title': 'Tech Giant Unveils Revolutionary Smartphone', 'category': 'Technology', 'readTime': 6}, {
  'title': 'Record-Breaking Heatwave Sweeps Across Europe', 'category': 'Environment', 'readTime': 4}, {
  'title': 'Blockbuster Movie Breaks Box Office Records', 'category': 'Entertainment', 'readTime': 3}, {
  'title': 'Stock Market Soars to All-Time High', 'category': 'Finance', 'readTime': 8
  } ]}
```

Set the base 'myData' variable equal to the existing data. Using this format allows for the next piece to be save to include and used with several pieces of data throughout a template.
```handlebars
{myData = articles} {* Set the base data here *}
```

Note: Zephyr requires this be on 1 line in real use.

This special code will create a new, more complete array with the position details.
```handlebars
{itemsWithContext = []}
{foreach myData as i,data}
    {currentItem = myData[i]}
    {nextItem = myData[i+1] != null ? myData[i+1] : myData[0]}
    {previousItem = myData[i-1] != null ? myData[i-1] : myData[length(myData)-1]}
    {firstItem = i == 0}
    {lastItem = i == length(myData)-1}
    {position = i + 1}
    {totalItems = length(myData)}
    {thisFullItem = {
        "current": currentItem,
        "next": nextItem,
        "previous": previousItem,
        "first": firstItem,
        "last": lastItem,
        "position": position,
        "totalItems": totalItems
    }}
    {push("itemsWithContext", thisFullItem)}
{/foreach}
```

## Usage

After preprocessing, you can use the enriched data in your template:

```handlebars
{foreach itemsWithContext as i, item}
  <div class="item">
    {if item.first}<strong>Featured Article</strong>{/if}   
    <h2>{item.current.title}</h2>
    <p>Category: {item.current.category}</p>
    
    {if !item.last}<a href="https://www.example.com/{replace(item.next.title,' ','-')}">Next: {item.next.title}</a> <br>{/if}
    
    {if !item.first}<a href="https://www.example.com/{replace(item.previous.title,' ','-')}">Previous: {item.previous.title}</a> <br>{/if}
  </div>
{/foreach}
```

This loops over each item, adding extra text for the first article, and adding links to the next and previous articles when they are available.

# More

This can be used in concert with the cycler for even more templating:

## Extra Setup

Cycler:
```handlebars
{cycler = ["left", "center", "right","center"]}
```

## Usage

```handlebars
{foreach itemsWithContext as i, item}
  {r = i % length(cycler)}
  <div class="item item-{cycler[r]}" style="text-align: {cycler[r]}">
       
    {if item.first}<strong class="highlight">Featured Article</strong>{/if}   
    <h2>{item.current.title}</h2>
    
    <p>Category: {item.current.category}</p>
    <p>Read time: {item.current.readTime} minutes</p>
    
    {if !item.last}<a href="https://www.example.com/{replace(item.next.title,' ','-')}" class="next-link">Next: {item.next.title}</a> <br>{/if}
    
    {if !item.first}<a href="https://www.example.com/{replace(item.previous.title,' ','-')}" class="prev-link">Previous: {item.previous.title}</a> <br>{/if}
  </div>
{/foreach}
```

This does the same as the first example and also adds the cyclic CSS positioning [from the basic example here](https://github.com/Colin-Whelan/SailthruCommunityDocs/blob/main/Code%20Samples/Advanced%20Zephyr/Cycler.md).


