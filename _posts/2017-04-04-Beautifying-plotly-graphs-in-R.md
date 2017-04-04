---
layout: post
title: Beautifying plotly graphs in R.
---
<p align="justify">Hello!
Till now I was styling the graphs in Photoshop rather than prepare them properly in RStudio (sooo lame, huh?) But I decided it's time to learn how to do it, so here is the little tutorial about making a plot beautiful using only R and plotly package. 

We'll gonna play with San Francisco average monthly temperatures availaible <a href="http://www.holiday-weather.com/san_francisco/averages/">here</a>.</p>

What we have to do before plotting a graph:
```r
a <- c(1:12)
b <- c(23, 28,	38, 54,	73, 42,	24, 27,	78, 63,	41, 51)
weather <- data.frame(month=a,temp=b)
weather
   month temp
1      1   10
2      2   11
3      3   11
4      4   11
5      5   13
6      6   13
7      7   14
8      8   14
9      9   14
10    10   14
11    11   13
12    12   10
```

<p align="justify">Quick  reminder  about plotly:

<code>plotly</code> charts are described declaratively in the call signature of <code>plotly::plot_ly</code>, <code>plotly::add_trace</code>, and <code>plotly::layout</code>. Every aspect of a plotly chart (the colors, the grid-lines, the data, and so on) has a corresponding key in these call signatures. This page contains an extensive list of these attributes.

Plotly's graph description places attributes into two categories: <code>traces</code> (which describe a single series of data in a graph) and <code>layout</code> attributes that apply to the rest of the chart, like the title, xaxis, or annotations).

Here is a simple example of a plotly chart inlined with links to each attribute's reference section.</p>
```r
library(plotly)

p <- plot_ly(economics,
             type = "scatter",        # all "scatter" attributes: https://plot.ly/r/reference/#scatter
             x = ~date,               # more about scatter's "x": /r/reference/#scatter-x
             y = ~uempmed,            # more about scatter's "y": /r/reference/#scatter-y
             name = "unemployment",   # more about scatter's "name": /r/reference/#scatter-name
             marker = list(           # marker is a named list, valid keys: /r/reference/#scatter-marker
               color="#264E86"        # more about marker's "color" attribute: /r/reference/#scatter-marker-color
             )) %>%
  layout(                        # all of layout's properties: /r/reference/#layout
         title = "Unemployment", # layout's title: /r/reference/#layout-title
         xaxis = list(           # layout's xaxis is a named list. List of valid keys: /r/reference/#layout-xaxis
            title = "Time",      # xaxis's title: /r/reference/#layout-xaxis-title
            showgrid = F),       # xaxis's showgrid: /r/reference/#layout-xaxis-showgrid
         yaxis = list(           # layout's yaxis is a named list. List of valid keys: /r/reference/#layout-yaxis
            title = "uidx")     # yaxis's title: /r/reference/#layout-yaxis-title
  )
 ```
 
<p align="justify">Having that in mind let’s create the simplest possible barplot with empty layout attribute:</p>
```r
p <- plot_ly(
    x = weather$month,
    y = weather$temp,
    type = "bar"
) %>%
layout()
```
<p align="center">
  <img src="/images/beautifying_plot/naked_plot.png">
</p>

<p align="justify">Now we can start styling it! <a href="https://plot.ly/r/reference/#layout">Here</a> you can check all possible layout properties.

#### Changing background color

<p align="justify">I’m gonna kick off with changing the background. To do so, we have to add 2 keys to <code>layout</code>:
<code>paper_bgcolor</code> (Sets the color of paper where the graph is drawn.) and  <code>plot_bgcolor</code> (Sets the color of plotting area in-between x and y axes.) I chose a nice blue.</p>

```r
layout(paper_bgcolor='#4666d1',
       plot_bgcolor='#4666d1')
```

<p align="center">
  <img src="/images/beautifying_plot/blue.png">
</p>



#### Changing bars color

<p align="justify">If you did too, you can now see that the plot became completely unreadable. Let’s fix it by changing bar’s color to white, adding <code>marker = list(color = '#ffffff')</code> (Sets the marker color. It accepts either a specific color or an array of numbers that are mapped to the colorscale relative to the max and min values of the array or relative to `cmin` and `cmax` if set.) to <code>plot_ly</code> part:</p>

```r
plot_ly(
    x = weather$month,
    y = weather$temp,
    type = "bar"
    marker = list(color = '#ffffff')
   ) %>%
   layout()
```

#### Styling axis

<p align="justify">Now we can proceed to styling X and Y axis.  We will continue adding the keys to the <code>layout</code> part, right after <code>plot_bgcolor</code>. As both xaxis and yaxis will have many arguments, we will wrap them in <code>list()</code>. See below:</p>

```r
layout(paper_bgcolor='#4666d1',
 	     plot_bgcolor='#4666d1',
       xaxis = list(place for arguments),
       yaxis = list(place for arguments)
)
```

First thing, let’s adjust the color and make it white:
```r
xaxis = list(
        color = '#ffffff'),
yaxis = list(
        color = '#ffffff')
```

So far, we have this:
<p align="center">
  <img src="/images/beautifying_plot/blue_plot.png">
</p>

<p align="justify">How about adding some text, so we would know what are we looking at? Let’s replace X axis values by month’s names. </p>

`weather$month <- c("Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")`

<p align="justify">Let’s make them a factor and add levels, so R knows that they have specific order. (You can read more about it in <a href="https://doyouevendata.github.io/Extra-Simple-GGPLOT2-Graphs-in-RStudio/">previous post</a>, section Factors and Levels)</p>

```r
factor(weather$month, levels = c("Jan", "Feb", "Mar", 
                                        "Apr", "May", "Jun", 
                                        "Jul", "Aug", "Sep", 
                                        "Oct", "Nov", "Dec"))
```                                        

<p align="justify">If you re-generate the plot, the month’s names should be visible under the bars.</p>

<p align="center">
  <img src="/images/beautifying_plot/months_prosto.png">
</p>

#### Rotating axis text

<p align="justify">I don’t like it that way, I prefer them rotated a bit. To rotate, add to xaxis a <code>tickangle</code> key.</p>

```r
xaxis = list(
        color = '#ffffff',
        tickangle = -45
),
```

<p align="center">
  <img src="/images/beautifying_plot/months_krzywo.png">
</p>

<p align="justify">Much better. We don’t need to add title to the x axis, I think we can easily guess what it represents, but y axis could use one, therefore we add <code>title</code> key to the yaxis part:</p>

```r
yaxis = list(
              color = '#ffffff',
              title = "degrees in Celsius")
 ```
 
<p align="justify">But surely you would say “hey, but I want to show Celsius degrees by printing this cute little circle, like everyone else!” No worries.  Change "degrees in Celsius" to "degrees in \U2103" and you have it. (why U2130 changes to the cute circle? <a href="http://www.fileformat.info/info/unicode/char/2103/index.htm">Here</a> is the answer.)</p>

#### Styling titles

<p align="justify">We nearly have it, we are only missing the general title and some font styling. Let’s add a <code>title</code> to the general layout part:</p>

```r
title = "Average Temperature: San Francisco"
```

<p align="justify">and choose font properties. Again, we will wrap arguments in <code>list()</code>. Read more about title font <a href="https://plot.ly/r/reference/#layout-title">here</a>.</p>

```r
titlefont = list(
                family = "Agency FB",
                size = 30,
                color = '#ffffff')
```            

<p align="justify">I chose the Agency FB font, you can use anything that is present in your operating system. Now what is left is to define the same font for both x and y axis:</p>

```r
font = list(
                family = "Agency FB",
                size = 25)
```   

<p align="justify">As you can see, my plot is too big and doesn’t fit to the window. Let’s add a 10px <code>margin</code>:</p>

```r
margin = 10
```

et voila:

<p align="center">
  <img src="/images/beautifying_plot/final.png">
</p>

The whole code:

```r
a <- c(1:12)
b <- c(10,	11,	11,	11,	13,	13,	14,	14,	14,	14,	13,	10)
weather <- data.frame(month=a,temp=b)
weather$month <- c('Jan', 'Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec')
weather$month <- factor(weather$month, levels = c("Jan", "Feb", "Mar", 
                                        "Apr", "May", "Jun", 
                                        "Jul", "Aug", "Sep", 
                                        "Oct", "Nov", "Dec"))

plot_ly(
  x = weather$month,
  y = weather$temp,
  type = "bar",
  marker = list(color = '#ffffff'),
   ) %>%
       layout(paper_bgcolor='#5875D5',plot_bgcolor='#5875D5',
              xaxis = list(
                      color = '#ffffff',
                      tickangle = -45),
              yaxis = list(
                      color = '#ffffff',
                      title = "degrees in \U2103"),
              title = "Average Temperature: San Francisco",
              titlefont = list(
                family = "Agency FB",
                size = 45,
                color = '#ffffff'),
              font = list(
                family = "Agency FB",
                size = 25),
              margin = 10
        )
```