# Cycler

This code partially mimics the cycler() function from Nunjucks, a similar Handlebars language. 
The cycler pattern allows you to iterate through a predefined set of values in a repeating cycle as you loop through an array. 
This is useful for creating alternating layouts, styles, or any repeating pattern in your email templates.

**Base Cycler**
```handlebars
{articles = [{'title':'test1'},{'title':'test2'},{'title':'test3'},{'title':'test4'}]}
{cycler = ["left","right"]}
{foreach articles as i,c}
{r = i % length(cycler)}
{c.title} -- {cycler[r]} <br>
{/foreach}
```

**Produces**:

test1 -- left 

test2 -- right 

test3 -- left 

test4 -- right 


## Implementations
Given this data

```handlebars
{articles = [
    {'title': 'Breaking News: Global Summit Announced', 'category': 'Politics', 'readTime': 5},
    {'title': 'New Study Reveals Benefits of Mediterranean Diet', 'category': 'Health', 'readTime': 7},
    {'title': 'Tech Giant Unveils Revolutionary Smartphone', 'category': 'Technology', 'readTime': 6},
    {'title': 'Record-Breaking Heatwave Sweeps Across Europe', 'category': 'Environment', 'readTime': 4},
    {'title': 'Blockbuster Movie Breaks Box Office Records', 'category': 'Entertainment', 'readTime': 3},
    {'title': 'Stock Market Soars to All-Time High', 'category': 'Finance', 'readTime': 8}
]}
```

Note: Zephyr requires this be on 1 line in real use.

With these cycler values:

```{cycler = ["left", "center", "right","center"]}```

Like this:
```handlebars
{foreach articles as i, article}
  {r = i % length(cycler)}
  <div class="article" style="text-align: {cycler[r]}">
    <h2>{article.title}</h2>
    <p>Category: {article.category}</p>
    <p>Read time: {article.readTime} minutes</p>
  </div>
{/foreach}
```

Produces rows of divs with zig-zagging text alignments(more CSS can be added for `div.article` for additional styling): 
```html
<div class="article" style="text-align: left">
  <h2>Breaking News: Global Summit Announced</h2>
  <p>Category: Politics</p>
  <p>Read time: 5 minutes</p>
</div>


<div class="article" style="text-align: center">
  <h2>New Study Reveals Benefits of Mediterranean Diet</h2>
  <p>Category: Health</p>
  <p>Read time: 7 minutes</p>
</div>


<div class="article" style="text-align: right">
  <h2>Tech Giant Unveils Revolutionary Smartphone</h2>
  <p>Category: Technology</p>
  <p>Read time: 6 minutes</p>
</div>


<div class="article" style="text-align: center">
  <h2>Record-Breaking Heatwave Sweeps Across Europe</h2>
  <p>Category: Environment</p>
  <p>Read time: 4 minutes</p>
</div>


<div class="article" style="text-align: left">
  <h2>Blockbuster Movie Breaks Box Office Records</h2>
  <p>Category: Entertainment</p>
  <p>Read time: 3 minutes</p>
</div>


<div class="article" style="text-align: center">
  <h2>Stock Market Soars to All-Time High</h2>
  <p>Category: Finance</p>
  <p>Read time: 8 minutes</p>
</div>
```

