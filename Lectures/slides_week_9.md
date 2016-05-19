CSSS 508, Week 9
===
author: Rebecca Ferrell
date: May 25, 2016
transition: rotate
width: 1100
height: 750


Today
===

* `ggmap` for mashing up maps with `ggplot2`
* Lab: visualizing restaurant safety over space and time


ggmap
===
type: section


ggmap
===

`ggmap` is a package that goes with `ggplot2` so that you can plot spatial data directly onto map images downloaded from Google Maps, OpenStreetMap, Stamen Maps, or Naver Map (Korea).

What this package does for you:

1. Queries servers for a map (`get_map`) at the location and scale you want
2. Plots the raster image as a `ggplot` object
3. Lets you add more `ggplot` layers like points, 2D density plots, text annotations
4. Additional functions for interacting with Google Maps (e.g. getting distances by bike)


One day of SPD incidents
===

In Week 5, we looked at types of incidents the Seattle Police Department responded to in a single day. Now, we'll look at where those were.


```r
library(ggplot2); library(ggmap); library(readr); library(dplyr)
```


```r
spd_raw <- read_csv("https://raw.githubusercontent.com/rebeccaferrell/CSSS508/master/Seattle_Police_Department_911_Incident_Response.csv")
```

Quick map plotting with qmplot
===


```r
qmplot(data = spd_raw, x = Longitude, y = Latitude, color = I("firebrick"), alpha = I(0.5))
```

<img src="slides_week_9-figure/quick_plot-1.png" title="plot of chunk quick_plot" alt="plot of chunk quick_plot" width="1100px" height="550px" />


You don't even need data with qmap
===


```r
qmap(location = "mary gates hall university of washington", zoom = 15, maptype = "watercolor", source = "stamen")
```

<img src="slides_week_9-figure/UW_plot-1.png" title="plot of chunk UW_plot" alt="plot of chunk UW_plot" width="1100px" height="550px" />

get_map
===

ggmap
===


Getting regions
===

Map styles
===

Adding points
===

Adding density layers
===

Labeling points
===
