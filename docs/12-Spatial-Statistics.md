# Spatial Statistics

One of the most powerful ways I use and present data is to explain spatial patterns in our data.  How does a product perform in Ohio versus Iowa?  What might be the underlying weather or soil causes of these data?  How do they vary with geography?  

Quantitative data and hard numbers are fantastic.  But as we have already seed with plots, visualizations can be much more engaging.  Our minds are evolved to recognize patterns -- in fact, we are so focused on looking for patterns that we need tools like statistics to keep us honest.  So a statistics-based plot or map is a very powerful way to convey information to your audience or customers.

This is a one of two brand-new units in Agronomy 513.  You might not think a statistics software like R might be equipped to work with spatial data, especially after spending the first 11 weeks working with some ugly code.  But R can readily work with shape files and rasters (think of a fertilizer application map), both creating and analyzing them.  We will learn how to overlay polygons to relate soil to yield, and how to create a application map based on gridded soil tests.

This unit will be light on calculations (yea!) and focus more on three key areas.  First, what is projection and why do we need to worry about it if all we want to do is draw a map or average some data?  

Second, what is a shapefile?  How do we make sure it's projection is correct for our analyses and for joining with other shapefiles?  How can layer the shapefile data with soil survey data readily accessible through R?  How can we create an attractive map with our shapefile data?

Finally, we will study rasters.  Rasters organize data in grids of cells that are of equal dimensions.  Using point data from a shapefile, we can use tools like kriging to interpolate (predict) the value of each cell in the raster, creating another kind of map we can use to understand spatial data trends.

## Projection (General)
One of the most challenging concepts for me when I began working with spatial data was *projection*.  To be honest, it is still is a challenging concept for me!  Projection describes how we represent points on the surface of the earth, which is spheroidal, using maps, which are flat.  

![](data-unit-12/images/437-mapping-projection-types.png)

As we can see in the figure above, projection differ in how they are positioned relative to the earth's surface.  Some are positioned relative to the equator, others might be centered between the equator and the poles, while yet others may be positioned at the poles.  Each map will represent the center of it's geography better than the edges.  
Each map is a compromise between the representation of boundaries (positions on the earth's surface) and the areas within those boundaries.  Maps that pursue the accurate representation of boundaries on the earth's surface are going to end up distorting the area of geographies outside the focal point of the map.  Maps that accurately represent areas are going to distort the position of geographic boundaries on the earth`s surface.  Thus, there are hundreds of differet projection systems, focused on different areas of the earth, and using different units to describe the position of boarders.

![from https://datacarpentry.org/r-raster-vector-geospatial/09-vector-when-data-dont-line-up-crs/](data-unit-12/images/map_usa_different_projections.jpg)

"Whoa, Marin", you may be thinking.  "I'm not trying to represent the world, the United States, Minnesota, or even my county!  It's just a freaking yield map!"  And you would be absolutely correct: none of these projection systems are going to vary much in how they represent the location or area of a single section of land.  

But, in working with spatial data from that field, you will encounter differences among systems in how they locate your field on the face of the earth.  Therefore, it is important we look at a few examples so you understand how to process those data.

We will start in the lower corner with WGS 84.  This is the geographic system with which most of you are probably familiar.  It is also how I roll with most of my analyses.  It's simplistic, but it works just fine for point geographies -- that is, single points on the earth's surface.

### WGS 84 (EPSG: 4236)
WGS 84 refers to "World Geodetic System"; 84 refers to 1984, the latest (!) revision of this system. WGS 84 uses the earth's center as its *origin*.  An origin is the reference point for any map -- each location is then geo-referenced according to its position relative to the origin.  In WGS 84, the position of each location is described by its angle, relative to the origin.  We usually refer to these angles as degrees latitude and longitude.

EPSG (EPSG Geodetic Parameter Dataset) is a set of many, many systems used to describe the coordinates of points on the Earth's surface. and how they are projected onto flat maps.  The EPSG stands for "European Petroleum Survey Group" -- presumably, for the purpose of locating oil fields.  4326 is the code that EPGS uses to represent the WGS 84 system.

We can map the continental United states using

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-1-1.pdf)<!-- --> 
This is the flat map with which most of us are familiar.  Latitude and longitude are drawn as parallel lines on this map.  The map data are in a shapefile, a format we encountered at the beginning of this course.  Let's look at the top few rows of this shapefile.


```
## Simple feature collection with 6 features and 1 field
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: -124.4096 ymin: 32.53416 xmax: -86.80587 ymax: 49.38436
## Geodetic CRS:  WGS 84
##   state_name                       geometry
## 1 California MULTIPOLYGON (((-118.594 33...
## 2  Wisconsin MULTIPOLYGON (((-86.93428 4...
## 3      Idaho MULTIPOLYGON (((-117.243 44...
## 4  Minnesota MULTIPOLYGON (((-97.22904 4...
## 5       Iowa MULTIPOLYGON (((-96.62187 4...
## 6   Missouri MULTIPOLYGON (((-95.76564 4...
```

This is a complex dataset, so we will use the Minnesota state boundary as an example.  In the map below, there are two objects.  The pin in the map represents the map origin.  The green dots indicate the Minnesota border.


```
## Warning in st_cast.sf(., "POINT"): repeating attributes for all sub-geometries
## for which they may not be constant
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-3-1.png)<!-- --> 

Zoom in on Minnesota and click around its borders.  You will notice two things.  First, each point is specified in latitude and longitude. Second, longitude (the first number) is always negative while latitude (the second number) is always positive.

The sign and size of geocordinates in a projection system is defined two things: 1) where it places its origin (its reference point for locating objects on the map) and 2) what measurement units it uses.  In the case of WGS 84, the origin is the intersection of the Prime Meridian and the Equator.  Since all of the continental United States is in the western hemisphere, every state will have a negative longitude and a positive latitude.  Since WGS 84 uses angles, the measurement units will be in degrees, which never exceed the range of (-180, 180) for longitude and (-90,90) for latitude.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-4-1.png)<!-- --> 

### Mercator (EPSG: 3857)
The Mercator System is commonly used to project data onto a sphere.  If you look at the map below, it is very similar (actually related) to the WGS 84 map above but you may be able to see a slight "dome illusion" to the way the map is displayed. This projection is regularly used by online mapping services.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-5-1.pdf)<!-- --> 

Looking at the top few rows of the Minnesota data points, we can see the units are not latitude and longitude.  In this projection, they are easting and northing: measures of the distance east and north of the origin.  Easting and northing are usually measured in meters


```
## Simple feature collection with 6 features and 1 field
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -10823490 ymin: 6274648 xmax: -10610960 ymax: 6274978
## Projected CRS: WGS 84 / Pseudo-Mercator
##     state_name                  geometry
## 1    Minnesota POINT (-10823487 6274978)
## 1.1  Minnesota POINT (-10790305 6274859)
## 1.2  Minnesota POINT (-10731801 6274859)
## 1.3  Minnesota POINT (-10683932 6274859)
## 1.4  Minnesota POINT (-10613307 6274648)
## 1.5  Minnesota POINT (-10610961 6274651)
```

The origin for the Mercator projection is again the intersection of Prime Meridian and Equator, so each Minnesota border point will have a negative value for easting and a positive value for northing.


### US National Atlas Equal Area (EPSG: 2163)
As the name suggests, coordinate systems like the US National Atlas Equal Area project data so that the areas of geographic objects are accurate in the map.
![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-7-1.pdf)<!-- --> 

This system, like the Mercator above, uses northing and easting units.  But whe we look at our Minnesota border coordinates, we now notice our easting vaues are positive!  What happened?


```
## Simple feature collection with 6 features and 1 field
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 202876 ymin: 448171.5 xmax: 342482.3 ymax: 454473.8
## Projected CRS: NAD27 / US National Atlas Equal Area
##     state_name                  geometry
## 1    Minnesota   POINT (202876 448171.5)
## 1.1  Minnesota POINT (224686.3 448890.9)
## 1.2  Minnesota POINT (263125.5 450495.2)
## 1.3  Minnesota POINT (294567.2 451996.1)
## 1.4  Minnesota POINT (340942.6 454381.6)
## 1.5  Minnesota POINT (342482.3 454473.8)
```
As you have likely guessed, our origin has change.  For this projection, our origin is in Central South Dakota.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-9-1.png)<!-- --> 





### UTM Zone 11N (EPSG: 2955)

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-10-1.pdf)<!-- --> 

```
## Simple feature collection with 6 features and 1 field
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: -128697 ymin: 3599652 xmax: 3013312 ymax: 5706611
## Projected CRS: NAD83(CSRS) / UTM zone 11N
##   state_name                       geometry
## 1 California MULTIPOLYGON (((351881.3 37...
## 2  Wisconsin MULTIPOLYGON (((2845962 548...
## 3      Idaho MULTIPOLYGON (((480645 4915...
## 4  Minnesota MULTIPOLYGON (((1941564 561...
## 5       Iowa MULTIPOLYGON (((2169056 494...
## 6   Missouri MULTIPOLYGON (((2302630 471...
```

Here are the coordinates for the Minnesota border again.

```
## Simple feature collection with 6 features and 1 field
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 1941564 ymin: 5618787 xmax: 2079676 ymax: 5658008
## Projected CRS: NAD83(CSRS) / UTM zone 11N
##     state_name                geometry
## 1    Minnesota POINT (1941564 5618787)
## 1.1  Minnesota POINT (1963162 5624612)
## 1.2  Minnesota POINT (2001185 5635247)
## 1.3  Minnesota POINT (2032277 5644167)
## 1.4  Minnesota POINT (2078155 5657549)
## 1.5  Minnesota POINT (2079676 5658008)
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-13-1.png)<!-- --> 


### Projection Summary
It is good to know some of these basic projections, but by far the most important concept of this unit is that it is important you are aware of the projection system that accompanies your spatial data. If you are assembing data from multiple shapefiles, as we will do below with soil and yield maps, you will need to account for the projections of each shapefile, to make sure they all have the same projection system.

In addition, different spatial data operations may prefer one projection system over the other. Operations that summarize areas will require projections that are based on area, not geometry.  Similarly, spatial tools like rasters (which divide an area into rectangles or squares), will prefer a system that is square.



## Shape Files

### Case Study: Soybean Yield in Iowa
This is not our first encounter with shapefiles -- we plotted our first shapefile map in the beginning of our course.  Let's return to that dataset!

```r
library(tidyverse)
library(sf)

corn_yield = st_read("data-unit-12/merriweather_yield_map/merriweather_yield_map.shp", quiet=TRUE)
```

We can examine this shapefile by typing its name.

```r
corn_yield
```

```
## Simple feature collection with 6061 features and 12 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -93.15474 ymin: 41.66619 xmax: -93.15026 ymax: 41.66945
## Geodetic CRS:  WGS 84
## First 10 features:
##     DISTANCE SWATHWIDTH VRYIELDVOL Crop  WetMass Moisture                 Time
## 1  0.9202733          5   57.38461  174 3443.652     0.00 9/19/2016 4:45:46 PM
## 2  2.6919269          5   55.88097  174 3353.411     0.00 9/19/2016 4:45:48 PM
## 3  2.6263101          5   80.83788  174 4851.075     0.00 9/19/2016 4:45:49 PM
## 4  2.7575437          5   71.76773  174 4306.777     6.22 9/19/2016 4:45:51 PM
## 5  2.3966513          5   91.03274  174 5462.851    12.22 9/19/2016 4:45:54 PM
## 6  3.1840529          5   65.59037  174 3951.056    13.33 9/19/2016 4:45:55 PM
## 7  3.3480949          5   60.36662  174 3668.554    14.09 9/19/2016 4:45:55 PM
## 8  2.6919269          5   65.85538  174 4090.685    15.95 9/19/2016 4:46:16 PM
## 9  3.1840529          5   85.21010  174 5217.806    14.74 9/19/2016 4:46:21 PM
## 10 3.1184361          5   69.64239  174 4260.025    14.65 9/19/2016 4:46:21 PM
##     Heading VARIETY Elevation                  IsoTime yield_bu
## 1  300.1584   23A42  786.8470 2016-09-19T16:45:46.001Z 65.97034
## 2  303.6084   23A42  786.6140 2016-09-19T16:45:48.004Z 64.24158
## 3  304.3084   23A42  786.1416 2016-09-19T16:45:49.007Z 92.93246
## 4  306.2084   23A42  785.7381 2016-09-19T16:45:51.002Z 77.37348
## 5  309.2284   23A42  785.5937 2016-09-19T16:45:54.002Z 91.86380
## 6  309.7584   23A42  785.7512 2016-09-19T16:45:55.005Z 65.60115
## 7  310.0084   23A42  785.7840 2016-09-19T16:45:55.996Z 60.37653
## 8  345.7384   23A42  785.6068 2016-09-19T16:46:16.007Z 65.86630
## 9  353.3184   23A42  785.7545 2016-09-19T16:46:21.002Z 85.22417
## 10 353.1584   23A42  785.8365 2016-09-19T16:46:21.994Z 69.65385
##                      geometry
## 1  POINT (-93.15026 41.66641)
## 2  POINT (-93.15028 41.66641)
## 3  POINT (-93.15028 41.66642)
## 4   POINT (-93.1503 41.66642)
## 5  POINT (-93.15032 41.66644)
## 6  POINT (-93.15033 41.66644)
## 7  POINT (-93.15034 41.66645)
## 8  POINT (-93.15029 41.66646)
## 9  POINT (-93.15029 41.66648)
## 10 POINT (-93.15029 41.66649)
```

The most useful shapefiles, in my experience, are presented in the "spatial feature" format above.  It is, essentially, a data frame, but with a single, special geometry column that contains multiple measures per row.  The geometry column is, if you will, composed of columns within a column.

Let's ignore the data for now and look at the information (the metadata) at the top of the output.  First, let's note the geometry type is POINT.  Each row of this datafile defines one point.  Shapefiles can be composed of all sorts of objects: points, lines, polygons, sets of multiple polygons, and so forth -- and shapefiles can be converted between formats

Creating a successful map includes telling R what kind of object we intend to draw.  So knowing the formate of a shapefile is critical!a helpful starting point.

Second, look at the geographic CRS.  CRS stands for Coordinate Reference System.  In this case, we are already in the standard WGS 84 format we discussed earler, so our units are latitude and longitude.

One of the things we will learn this lesson is to use *Leaflet* to create maps.  Leaflet is an awesome applet whose true appreciation would require using four-letter conjunctions inappropiate for the classroom.  It creates interactive maps that can zoom in, zoom out, and identify the values of individual points.


```
## Warning: package 'RColorBrewer' was built under R version 4.1.3
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-16-1.png)<!-- --> 


### SSURGO
The Soil Survey Geographic Database (SSURGO) is maintained by the United States Department of Agriculture.  It contains extensive soil surveys: soil evaluations for properties, susceptibility to weather extremes, suitability for agriculture, recreation, and buildings.  The soil survey used to only be available in county books, which only special libraries had.  Now, you can access all these data through R in seconds and match them precisely to a given map location.

The SSURGO data is in a database.  A database is a series of tables, all describing different aspects of a data information.  Each table contains 1-3 columns that are keys to match the tables with each other.  Descriptions of the tables an their data can be obtained for SSURGO at:

https://data.nal.usda.gov/system/files/SSURGO_Metadata_-_Table_Column_Descriptions.pdf

Putting all these tables together can be messy -- fortunately, you only need to do it once, after which you can just change the shapefile you feed to the code.  I will give you that code in the exercises this week.




Here is a SSURGO map of soil organic matter.



![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-19-1.png)<!-- --> 


Here is another map, this time with the percent clay.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-20-1.png)<!-- --> 


Now that we have our SSURGO data, we can join it with our yield data and ask questions how yields were grouped by quantitative descriptors, such as soil map unit name ("muname"), texture ("texdesc"), drainage class ("drainagecl"), or parent material ("pmkind").


```
##   yield_bu                                 muname hzname sandtotal.r
## 1 65.97034 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
## 2 64.24158 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
## 3 92.93246 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
## 4 77.37348 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
## 5 91.86380 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
## 6 65.60115 Wiota silt loam, 0 to 2 percent slopes     H1         9.4
##   silttotal.r claytotal.r om.r awc.r ksat.r cec7.r    chkey   texdesc
## 1        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
## 2        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
## 3        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
## 4        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
## 5        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
## 6        67.1        23.5    4  0.22      9   22.5 59965160 Silt loam
##     drainagecl slope.r   pmkind                   geometry
## 1 Well drained       1 Alluvium POINT (-93.15026 41.66641)
## 2 Well drained       1 Alluvium POINT (-93.15028 41.66641)
## 3 Well drained       1 Alluvium POINT (-93.15028 41.66642)
## 4 Well drained       1 Alluvium  POINT (-93.1503 41.66642)
## 5 Well drained       1 Alluvium POINT (-93.15032 41.66644)
## 6 Well drained       1 Alluvium POINT (-93.15033 41.66644)
```

For example, here are soybean yields by soil texture, which would suggest a trend where soil yield increased with clay content in this field.


```
## # A tibble: 4 x 2
##   texdesc         yield_bu
##   <chr>              <dbl>
## 1 Clay loam           81.6
## 2 Silty clay loam     80.7
## 3 Silt loam           79.2
## 4 Loam                78.0
```


And this table would suggest that soybean preferred poorly drained soil to better-drained soils.

```
##                drainagecl yield_bu
## 1          Poorly drained 81.32400
## 2            Well drained 78.47489
## 3 Somewhat poorly drained 78.09084
```

### Operations with Shapes
Above, we subsetted our yield data according to different soil properties.  In some cases, however, we may want to subset or group data by location.

#### Intersection
Say, for example, we applied a foliar fertilizer treatment to part of the field, as shown in the map below.


```
## [1] "temp\\file55b060fd1edd"
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-24-1.png)<!-- --> 

How might we find out statistics for yield measures within that applied area?

```
## Warning: attribute variables are assumed to be spatially constant throughout all
## geometries
```

```
## [1] 79.74649
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-25-1.png)<!-- --> 

#### Difference
What about the yields outside that area?

```
## Warning: attribute variables are assumed to be spatially constant throughout all
## geometries
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-26-1.png)<!-- --> 



```r
mean(field_yield_outside$yield_bu)
```

```
## [1] 80.12065
```

#### Union

What if we had two field plot areas?
![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-28-1.png)<!-- --> 

If we wanted to analyze two areas together, we could use st_union() to combine them:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-29-1.png)<!-- --> 



```r
mean(corn_yield$yield_bu)
```

```
## [1] 80.09084
```


## Rasters











```r
library(stars)

selected_data = point_data %>%
  filter(attribute=="P_bray")

### make grid
grd = st_bbox(boundary) %>%
  st_as_stars() %>%
  st_crop(boundary)
# %>%
  # st_set_crs(6505)


# ordinary kriging --------------------------------------------------------
v = variogram(measure~1, selected_data)
m = fit.variogram(v, vgm("Sph"))
kridge_plot = plot(v, model = m)

lzn.kr1 = gstat::krige(formula = measure~1, selected_data, grd, model=m)
```

```
## [using ordinary kriging]
```

```r
# plot(lzn.kr1[1])

library(leafem)

soil_p_map = leaflet(lzn.kr1[1]) %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5) 
```




![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-34-1.png)<!-- --> 









In the previous section, we worked with what are often described as vectors or shapes.  That is, points which may or may not have been connected to form lines or polygons.

A raster is a grid system that we use to describe spatial variation.  In essence, it is a grid system.  Here is the same field we worked with in the previous section, now overlaid with a grid:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-38-1.png)<!-- --> 

If you think it looks a little like we took a spreadsheet and trimmed it to fit our field, you are exactly right.  Taking this analogy further, just as a spreadsheet is composed of cells, each containing a different value, so is a raster.  Here it is, filled in with values representing predicted soil P test values:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-39-1.png)<!-- --> 

Often, the cells would be colored along a given gradient (say red-yellow-green) according to their values.  This helps us to see spatial trends.


### Interpolation
To create a raster that represents continuous soil trends across a field, however, we need to do a little modelling.  You see, we start out with a set of soil cores like this:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-40-1.png)<!-- --> 

But we are trying to make predictions for each cell of our raster:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-41-1.png)<!-- --> 
Some cells above one measure, others split a measure with a neighboring cell, and yet others contain no measure at all.  In addition, even cells that contain a measure may have it in different locations relative to the cell center.

When we interpolate a raster, we make an educated guess about the values of cells in which no measure was taken, based on the values of the other cells.  In the following example, the middle cell is missing:

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-42-1.pdf)<!-- --> 

The most basic way to interpolate this missing value would be to estimate it's value as the mean value of all cells adjacent to it (dark grey in plot above).   So the value of the missing cell would be equal to:

$$ \text{cell value} = \text{mean}(16.6, 17.9, 13.1, 19.1, 11.6, 11.1, 16.2, 16.2) = 16.6 $$

If we wanted to be a bit more accurate, we might extend out to the next ring of cells around the cell we are trying to estimate.  But we probably would not want them to factor into the mean calculation as much as the first ring of cells -- the difference betwen two measurements tends to increase with distance.  So if they are two units away from the missing cell of, we might weight them so they contribute 1/4th as much to the estimate as the immediately adjacent cells.

If we were to fill out a table, it would look like this:


```
## # A tibble: 25 x 6
##      row   col  cell value weight weighted_value
##    <int> <int> <int> <dbl>  <dbl>          <dbl>
##  1     1     1     1  18.1   0.25           4.53
##  2     2     1     2  20.1   0.25           5.03
##  3     3     1     3  21.8   0.25           5.45
##  4     4     1     4  17.5   0.25           4.38
##  5     5     1     5  21.6   0.25           5.4 
##  6     1     2     6  21.1   0.25           5.28
##  7     2     2     7  14.3   1             14.3 
##  8     3     2     8  17.3   1             17.3 
##  9     4     2     9  10.3   1             10.3 
## 10     5     2    10  24.8   0.25           6.2 
## # ... with 15 more rows
```

The weighted value for each cell is the product of its observed value times its weight.  To calculate the weighted value, we sum the weights and the weighted values.  The weighted mean is then:

$$ \text{weighted mean} = \frac{\sum{\text{weighted value}}}{\sum{\text{weight}}} $$


In this example, the calculation would look like:

$$ \text{weighted mean} = \frac{220.45}{13} = 17.0$$

What we have just calculated is called the *inverse distance-weighted (IDW) mean* of the surrounding points.  It is a simple, elegant way to estimate the missing value.

### Kriging
The inverse distance-weighted mean, however, is not as accurate as which we are capable.  We assume that the influence of points away from the empty cell decreases exponentially with distance.  We don't consider how we would optimally weight the values of the surrounding cells.

We can develop a more complex, but likely accurate, estimate of the cell value using a different interpolation practice called *kriging*.  (For some reason, I always want to insert a "d" into this term so it rhymes with "bridging".  But it is pronounced KREE-ging.)  The name comes from Danie Krige, a South African geostatician who was interested in locating gold deposits.

I will take you through the basics of kriging.  A more elegant explanation of kriging can be found here: https://pro.arcgis.com/en/pro-app/tool-reference/3d-analyst/how-kriging-works.htm

Kriging evaluates how points of varying differences from each other are correlated. These correlations are plotted in a *semivariogram*.

![from "How Kriging Works" (https://pro.arcgis.com/en/pro-app/tool-reference/3d-analyst/how-kriging-works.htm)](data-unit-12/images/range_sill_nugget.jpg)

X is the distance between point pairs.  Y is the squared difference between each pair of points selected by the software.  Sometimes, pairs will be binned (similar to the binning in histrograms) into according to distance (called "lag") in this analysis.  The squared differences of all pairs within a lag bin are averaged.

Does this nonlinear function look familiar by any chance?  That's right, it is a monomolecular function!  The curve is described by different terms to which we are used to (and, to be honest, they don't always make much sense.)  In the kriging curve, the *Sill* is the maximum value the curve approaches.  The *Nugget* is the y-intercept.

Otherwise, this curve is fit with nonlinear regression, just like the others we have seen.  The calculated semivariances are then used to weight observations in calculating the weighted me.  In this way, observations are weighted according the the strength of surrounding measurements, according to their measured correlation with distance.

Here is the kridge plot for our soil phosphorus test data.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-45-1.pdf)<!-- --> 
Using this model of the relationship between covariance and distance, then, R can determine how much to weight each observation, based on distance, to estimate values between measurement points.

When we produced our initial raster, the cell size was especially large for simplification of the raster, and to allow the cell values to be shown.  When we build a raster with kriging, however, the cell size can be very small.  This has the effect of getting rid of blockiness and allowing us to better predict and visualize trends in values across the landscape.

Here is our soil phosphorus test map, interpolated by R, using kriging. The red areas are areas of lower P test values.  The green and blue areas have the greatest test values, and the yellow areas are intermediate.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-46-1.png)<!-- --> 

### Operations on Kriged Data
Of course, to take this example to completion, we would like to know the P-allication rate for each of the cells within our raster.  The University of Minnesota recommended application rate, based on soil P value and yield goal, is:

$$ P _2 O _5 \text{ recommended rate} = [0.700 - .035 (\text{Bray P ppm})] (\text{yield goal}) $$

We can plug each cell's Bray P test value into this equation.  For example, a point with a test value of 17 would, given a yield goal of 200, have a $P_2O_5$ rate recommendation of:

$$ P _2 O _5 \text{ recommended rate} = [0.700 - .035 (17)] (\text{200}) = 21$$

Here is the rate recommendation map.

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-47-1.png)<!-- --> 

The blue areas now have the highest recommended rates.  Yellow are intermediate.  Red areas should receive no P fertilizer.




## Exercise: Shape Files
If you are downloading data files from your tractor, applicator, or combine, they are probably in shape file format.  Although I will refer to shape file in the singular, a shape file is typically bundled with multiple other files, with the same file name but different extensions: .dbf, .prj, and .shx.  Of these three, the .prj is perhaps the most interesting, as it can be opened to view the projection used to georeference the records in the shape file.

### Case Study
We will work with another soil test shapefile from Minnesota.

```
## Reading layer `Burnholdt_grid_sample' from data source 
##   `C:\ds_ag_professionals\data-unit-12\Burnholdt_grid_sample.shp' 
##   using driver `ESRI Shapefile'
## Simple feature collection with 1150 features and 9 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -92.72799 ymin: 43.95069 xmax: -92.71914 ymax: 43.95704
## Geodetic CRS:  WGS 84
```

```
## Simple feature collection with 6 features and 9 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -92.72784 ymin: 43.95342 xmax: -92.72423 ymax: 43.95438
## Geodetic CRS:  WGS 84
##   obs Sample_id SampleDate ReportDate P2    Grower     Field attribute measure
## 1 107        10  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     5.6
## 2 108        11  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     5.2
## 3 109        12  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     4.7
## 4 110        13  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     4.3
## 5 111        14  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     5.1
## 6 112        15  6/20/2019  6/20/2019  0 Burnholdt Burnholdt        Om     4.1
##                     geometry
## 1 POINT (-92.72784 43.95342)
## 2 POINT (-92.72672 43.95342)
## 3 POINT (-92.72555 43.95343)
## 4 POINT (-92.72423 43.95343)
## 5 POINT (-92.72425 43.95433)
## 6 POINT (-92.72545 43.95439)
```

It is always important to inspect datasets before we analyze them.  A particularly important objective in inspecting a shape file dataset is to see what type of *geometry* it includes: POINT, POLYGON, etc.  Some equipment will use POINT geometry -- ie a single georeference to indicate where the center of the equipment was at the time of measure.  Others will use polygons to represent the width and length of the equipment.  Personally, I find the latter format to be overkill, but I have come across it.  (If you ever run into issues with that format, please feel free to contact me.)

We can confirm our measures here are referenced with POINT geometry.

In this exercise, we want to look at soil organic matter (attribute = Om in this file.), so let's isolate those measures.  We can do so using the *filter()* function


```r
single_attribute_data = soil_test_data %>%
  filter(attribute=="Om")
```


We are now ready to create our first map of the data.  We will use that wonderful mapping application, leaflet.  The code below consists of four lines.

1) "single_attribute_data" tells R which dataset we are going to use to build the map.
2) "leaflet()" tells R to use that app to build the map
3) "addCircleMarkers" tells R to draw point geometries.  There are many other arguments we will add later to tell R what colors to use, marker size, and what value (if any) to show when the user clicks on a marker.
4) "addTiles()" tells R to use a street background for the map.

Well, that's cool, but what do the points mean?  Let's add some color to this.  To do this, we need to tie the color of the circles to the organic matter level.  We need to create a palette, which we can do with the *colorBin()* function.

My favorite pallette is the red-yellow-green palette we are used to in yield maps.  We will load the *RColorBrewer* package, which defines both individual colors and palettes.  We will then tell R in the colorBin command to use this palette.


```r
library(RColorBrewer)
palette_om = colorBin(palette = "RdYlGn", single_attribute_data$measure)
```

We can then color code our points by adding the *fillColor* argument to our plot code.


```r
library(leaflet)

m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_om(measure)
  ) %>%    # adds markers as points
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-51-1.png)<!-- --> 




Let's make a few more tweaks.  First, the colors are so transparent the wash out, so let's increase their opacity using the argument of that name.

```r
m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_om(measure),
    fillOpacity = 0.8
  ) %>%    # adds markers as points
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-52-1.png)<!-- --> 


Next, the blue border detracts from the cells.  We can set its weight to zero so it disappears.


```r
m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_om(measure),
    fillOpacity = 0.8,
    weight = 0
  ) %>%    # adds markers as points
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-53-1.png)<!-- --> 


Let's make the points a little smaller using the *radius* argument, so they don't so overwhelm the field.

```r
m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_om(measure),
    fillOpacity = 0.8,
    weight = 0,
    radius=6
  ) %>%    # adds markers as points
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-54-1.png)<!-- --> 



Finally, let's add a legend.  We provide a minimum of two arguments to it.  First *values* tells it that the legend will correspond to the values of measure in our dataset.  Second, *pal =* tells R to use the palette we developed for organic matter.


```r
m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_om(measure),
    fillOpacity = 0.8,
    weight = 0,
    radius=6
  ) %>%    # adds markers as points
  addTiles() %>%
  addLegend(values = ~measure,
            pal = palette_om)

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-55-1.png)<!-- --> 

There are thousands of other ways to modify this plot, but this little bit should get you far!



### Practice
Using the code above, plot other soil variable.  Run the code below to see a list of the many different attributes you can examine.

```r
unique(soil_test_data$attribute)
```

```
##  [1] "Om"       "Ph"       "Bph"      "No3"      "Salinity" "P"       
##  [7] "P_bray"   "P_olsen"  "M3_p"     "M3icp_p"  "K"        "Ca"      
## [13] "Mg"       "Na"       "S"        "Zn"       "Cu"       "Mn"      
## [19] "Fe"       "B"        "Cec"      "Ca_sat"   "Mg_sat"   "K_sat"   
## [25] "H_sat"
```

Experiment with plugging and playing with the above code. (There is *never* shame in re-using code!!!).  Play with the settings for fillOpacity, weight, and radius until they are to your liking.  Also, try some different palettes: "RdBu", "PRgn", or others (list available at https://www.r-graph-gallery.com/38-rcolorbrewers-palettes.html).


```r
palette_soil = colorBin(palette = "RdYlGn", single_attribute_data$measure)

m <- single_attribute_data %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_soil(measure),
    fillOpacity = 0.8,
    weight = 0,
    radius=6
  ) %>%    # adds markers as points
  addTiles() %>%
  addLegend(values = ~measure,
            pal = palette_soil)

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-57-1.png)<!-- --> 





## Exercise: SSURGO
In this lesson, we saw how the Soil Survey Geographic Database (SSURGO) database can be accessed to gain further insight into fields.

This isn't an exercise so much as a demonstration how those data can be obtained and plotted through R.  It is hear for your use or reference, but not a required part of this lesson.

We can load our shapefile just as we have done before.  For reasons explained below, it is important to name the dataset "field".


```r
library(sf)
library(tidyverse)
library(leaflet)

field = st_read("data-unit-12/exercise_data/yield shape.shp", quiet=TRUE)
```


First, let's create a yield map, just as we learned to in the "shapefile" exercise.  If you have not completed that exercise, please do so before continuing.


```r
palette_yield = colorBin("RdYlGn", field$Yld_Vol_Dr)

m <- field %>%   # starts with om dataset
  leaflet() %>%    # starts leaflet
  addCircleMarkers(
    fillColor = ~palette_yield(Yld_Vol_Dr),
    fillOpacity = 0.8,
    weight = 0,
    radius=0.1
  ) %>%    # adds markers as points
  addTiles() %>%
  addLegend(values = ~Yld_Vol_Dr,
            pal = palette_yield)

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-59-1.png)<!-- --> 


This is the one and only time in this course when I will ask you just to run a chunk of code without explaining it line-by-line.  As long as your spatial feature object (the field shapefile) is named "field", just run this chunk as it is.


```r
##### Run this as-is: Do not modify !! #######



library(FedData)
library(sp)
soil_data_sp = as(field, "Spatial")
soil_map = get_ssurgo(soil_data_sp, "example")
soil_sf = st_as_sf(soil_map$spatial) %>%
  st_set_crs(4326)

mu_point = soil_map$tabular$mapunit %>%
  dplyr::select(musym, muname, mukey)

# get measures of sand, silt, clay, etc
horizon_diagnostics = soil_map$tabular$chorizon %>%
  dplyr::select(hzname, sandtotal.r, silttotal.r, claytotal.r, om.r, awc.r, ksat.r, cec7.r, cokey, chkey)

# soil texture class
texture = soil_map$tabular$chtexturegrp %>%
  filter(rvindicator=="Yes") %>%
  dplyr::select(texdesc, chkey)

# percent slope and drainage class
slope = soil_map$tabular$component %>%
  dplyr::select(drainagecl, slope.r, mukey, cokey)

parent_material = soil_map$tabular$copm %>%
  filter(pmorder==1 | is.na(pmorder)) %>%
  dplyr::select(pmkind, copmgrpkey, copmkey)

pm_group = soil_map$tabular$copmgrp %>%
  dplyr::select(cokey, copmgrpkey)

component = soil_map$tabular$component %>%
  dplyr::select(mukey, cokey)

all_soil_data = mu_point %>%
  left_join(component) %>%
  left_join(horizon_diagnostics) %>%
  group_by(mukey) %>%
  filter(chkey==min(chkey)) %>%
  ungroup() %>%
  left_join(texture) %>%
  left_join(slope) %>%
  left_join(pm_group) %>%
  left_join(parent_material) %>%
  dplyr::select(-c(copmgrpkey, copmkey, musym, cokey))

complete_soil_data = soil_sf %>%
  rename_all(tolower) %>%
  mutate(mukey = as.numeric(mukey)) %>%
  left_join(all_soil_data)
```








The soil variables included in the *complete_soil_data* datset are:

"sandtotal.r"   -   Percent Sand
"silttotal.r"   -   Percent Silt
"claytotal.r"   -   Percent Clay
"om.r"          -   Percent Organic Matter
"awc.r"         -   Available Water Holding Capacity
"ksat.r"        -   Saturated Hydraulic Conductivity (drainage)
"cec7.r"        -   Cation Exchange Capacity
"texdesc"       -   Soil Textural Class (based on percent sand, silt, anda clay)
"drainagecl"    -   Drainage Class (based on hydraulic conductivity)
"slope.r"       -   Slope (percent change per 100 ft travelled)


We can plot any of the above variables by referencing them as "complete_soil_data$" plus the variable of interest.

We can then create a leaflet plot as we have elsewhere in this unit.

```r
library(grDevices)
pal_om = colorNumeric("RdYlGn", complete_soil_data$om.r)

m <- complete_soil_data %>%
  leaflet()  %>%
  addCircleMarkers(
    radius = 4,
    weight=0,
    fillColor = ~ pal_om(om.r),
    opacity = 0.8,
    popup = as.character(complete_soil_data$om.r)
  ) %>%
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-63-1.png)<!-- --> 



## Exercise: Rasters
In the lecture, we learned that rasters were organized within a grid system, with each cell representing a unique value.  Cells may by colored according to which "bin" their value falls, in order to facilitate the identification of spatial patterns.

### Case Study: Soil Test Data
The following data were collected from a field in Minnesota.  We will create a lime application map.  To start , we need to load two shape files.  The first shape file contains our soil samples.


#### Load and Filter Data

```r
library(tidyverse)
library(gstat)
library(sp)
library(raster)
library(stars)
library(leaflet)
library(leafem)


point_data = st_read("data-unit-12/Folie N & SE_grid_sample.shp", quiet=TRUE)
head(point_data)
```

```
## Simple feature collection with 6 features and 9 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -92.79313 ymin: 43.98697 xmax: -92.78942 ymax: 43.99318
## Geodetic CRS:  WGS 84
##   obs Sample_id SampleDate ReportDate P2    Grower        Field attribute
## 1  29        21 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
## 2  30        22 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
## 3  31        27 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
## 4  32         5 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
## 5  33         6 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
## 6  34         8 10/26/2018 10/30/2018  0 Tom Besch Folie N & SE        Om
##   measure                   geometry
## 1     3.2  POINT (-92.78954 43.9931)
## 2     3.8   POINT (-92.7895 43.9926)
## 3     3.3 POINT (-92.78942 43.98697)
## 4     5.9 POINT (-92.79313 43.99245)
## 5     3.1 POINT (-92.79294 43.99318)
## 6     1.8  POINT (-92.79145 43.9902)
```

We can see this dataset, much contains POINT geometries -- that is, each row of data is related to a single georeference.

Our second shape file describes the field boundary.  This is very important too -- otherwise we would not know what shape and size our raster should be.


```r
boundary = st_read("data-unit-12/Folie N &SE_boundary.shp", quiet=TRUE)

head(boundary)
```

```
## Simple feature collection with 1 feature and 4 fields
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: -92.79768 ymin: 43.98635 xmax: -92.78861 ymax: 43.99362
## Geodetic CRS:  WGS 84
##    FieldName MapsCode    Grower       Field                       geometry
## 1 NORTH & SE CIT18NW1 Tom Besch Folie N &SE MULTIPOLYGON (((-92.79234 4...
```

We see the field contains POLYGON geometry and a single row of data, which mainly describes the field name.

Our last step in this section is to filter the data so they only contain buffer pH data.


```r
selected_data = point_data %>%
  filter(attribute=="Bph")
```

Without going through all the trouble of building out a map, we can still check the location of our points using leaflet().


```r
m <- selected_data %>%
  leaflet() %>%
  addCircleMarkers() %>%
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-67-1.png)<!-- --> 


#### Build a Grid
To create a raster that matches the size and shape of our field, we need need to create a frame for R to fill in.  We can do this really easily with the following few lines of code from the *stars* package.  We will name our new raster "grd" and build it from our boundary dataset.  To start, we create a rectangular grid based on the boundary box.  This box extends from the point with the lowest X and Y value in a geometry to the point with the greatest X and Y value.  We can determine these points for our boundary using the *st_bbox* function.


```r
library(stars)

st_bbox(boundary)
```

```
##      xmin      ymin      xmax      ymax 
## -92.79767  43.98635 -92.78861  43.99362
```

The st_as_stars() function coverts the shapefile to a grid for us to fill in with our raster.  The last line uses the *st_crop* function to trim our rectangular raster to the shape of our field.


```r
library(stars)

### make grid
grd = st_bbox(boundary) %>%
  st_as_stars() %>%
  st_crop(boundary)
```

We can plot the grid using leaflet.  We create a generic map using the *addProviderTiles()* function.  Lastly, we add the grid using *addStarsImage()*.

```r
m <- grd %>%
  leaflet()  %>%
  addTiles() %>%
  addStarsImage()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-70-1.png)<!-- --> 

We won't be able to see the individual cells -- they are tiny -- but we can see it matches the shape of our field.

#### Build the Semivariogram
There are three steps to building a semivariogram:

1) Define relationship between the measure and distance with the *variogram* function.  This is much like defining a linear model before we summarise it.
2) Fit variogram model using the *fit.variogram* function from the *gstat* package.
3) Visually confirm the model approximately fits the data, by using the *plot* function to show the individual variances (v) and the model (m).


```r
v = variogram(measure~1, selected_data)
m = fit.variogram(v, vgm("Sph"))
```

```
## Warning in fit.variogram(v, vgm("Sph")): singular model in variogram fit
```

```r
plot(v, model = m)
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-71-1.pdf)<!-- --> 
The *fit.variogram* function takes two arguments.  The first is the name of the data.frame to which the distance, variance ("gamma"), and other statistics should be saved.

The second argument, vgm("Sph"), tells R to fit the variances with a spherical model.  There are several different models that could be used, but the spherical model is perhaps the most common.

The model appears to approximately fit the data, so we move to the next step: kriging.

#### Kriging
Kriging is where the map magic happens.  We use our variogram model to predict how related each empty cell in the raster is to each filled cell in the raster, based on their distance.  The strength of that relationship is then used to weight the filled cells as they are averaged to predict the value of each empty cell.

This magic happens with a single line of code, using the *krige* function of the *gstat* package.  There are four arguments to this function:

1) formula: the relationship we are modelling.  "measure ~ 1" means we are modelling the relationship between our measure value and location.  The 1 indicates that the minimum measured value does not have to be equal to zero
2) selected_data: the data that are being kriged
3) grd: the object we created above that contains the framework or templae for the raster we want to create
4) model=m: tells R to use the semivariogram model, m, we created and inspected above



```r
library(gstat)
kriged_data = gstat::krige(formula = measure~1, selected_data, grd, model=m)
```

```
## [using ordinary kriging]
```

The object that is returned is a list.  The predicted values are found in the first element of that list.  For the sake of clarity, let's extract that part of the list and rename it.


```r
finished_data = kriged_data[1]
```

#### Mapping Kriged Data
We will again use leaflet to map our data.  We will start out by creating our map and adding provider tiles.


```r
m <- finished_data %>%
  leaflet() %>%
  addTiles()

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-74-1.png)<!-- --> 

We can now add our raster (stars) image to the map using addStarsImage.  We will set the opacity=0.5 so we can see both the field and the test zones.

We will also set the colors to RdYlGn; we need to load the *RColorBrewer* package to use that palette.

```r
library(RColorBrewer)

m <- finished_data %>%
  leaflet() %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5,
                colors="RdYlGn")

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-75-1.png)<!-- --> 




#### Building the Recommendation
Our recommendation only requires two more lines of code.  We will create a new column, "rec", which will consist of the predicted lime application rate based on buffer pH.  If we look at University of Minnesota recommendations, we can see that the recommended rate of 100% effective neutralizing lime is equal to;

$$rate = 37000 - bpH \times 5000   $$

https://extension.umn.edu/liming/lime-needs-minnesota


So lets go ahead and do this.  We will plug "var1.pred" -- the generic raster name for the predicted variable -- into the equation above and solve for the recommended rate.


```r
finished_data$rec = (37000 - finished_data$var1.pred*5000)
```

One last thing: if you look at the recommendation table, however, you see the recommended rate is not given for buffer pH above 6.8.  Thus, if the recommendation is less than 3000, we should set it to zero.  Thus we need to use another function, *if_else*.

The if_else function defines the valuable of a variable, based on three arguments:

1) the condition to be tested
2) the value of the variable if the condition is true
3) the value of the variable if the condition is false


```r
finished_data$rec = if_else(finished_data$rec<3000, 0, finished_data$rec)
```

In the line above, we revise the value of the rec column.  If it is less than 3000, we set the rec to zero.  If it is 3000 or greater, we use it as-is.

#### Mapping the Recommendation
Finally, we can map the recommendation, similar to how we mapped the test levels above.


```r
m <- leaflet(finished_data["rec"]) %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5,
                colors = "RdYlGn")

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-78-1.png)<!-- --> 


The green areas above are the ones we should "Go" on -- lime should be applied to them.


### Practice 1
Take the map above and see if you can create soil K raster for the data above.

1) change the selected data below from "Bph" to "K".

```r
selected_data = point_data %>%
  filter(attribute=="Bph")
```

2) Create the raster framework.

```r
### make grid
grd = st_bbox(boundary) %>%
  st_as_stars() %>%
  st_crop(boundary)
```

3) Create and examine the variogram.  The model should roughly fit the variances.

```r
v = variogram(measure~1, selected_data)
m = fit.variogram(v, vgm("Sph"))
```

```
## Warning in fit.variogram(v, vgm("Sph")): singular model in variogram fit
```

```r
plot(v, model = m)
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-81-1.pdf)<!-- --> 

4) Krige the data and extract the predicted (or interpolated) values,

```r
kriged_data = gstat::krige(formula = measure~1, selected_data, grd, model=m)
```

```
## [using ordinary kriging]
```

```r
finished_data = kriged_data[1]
```

5) Create the map of soil K values!

```r
m <- finished_data %>%
  leaflet() %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5,
                colors="RdYlGn")

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-83-1.png)<!-- --> 



6) Calculate recommended application rates, assuming a 200 bushel/acre yield target.
Change the code below accordingly.  The K recommendation formula is:

K recommendation = (1.12 - 0.0056*finished_data$var1.pred)*200

just plug that into the first line of code.  K is not recommended for locations that had a soil test greater than 200 ppm, so adjust your recommendation accordingly using the second line of code.


```r
finished_data$rec = (37000 - finished_data$var1.pred*5000)
finished_data$rec = if_else(finished_data$rec<3000, 0, finished_data$rec)
```

7) Plot the recommendated rates:

```r
m <- leaflet(finished_data["rec"]) %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5,
                colors = "RdYlGn")

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-85-1.png)<!-- --> 




### Practice 2
Take the map above and see if you can create soil test pH raster for the data above.  You should only need to change "selected_data" to select "Ph".  There is no recommendation rate to create.

1) Filter the attribute for "pH".

```r
selected_data = point_data %>%
  filter(attribute=="Ph")
```

2) Create the raster framework.

```r
### make grid
grd = st_bbox(boundary) %>%
  st_as_stars() %>%
  st_crop(boundary)
```

3) Create and examine the variogram.  The model should roughly fit the variances.

```r
v = variogram(measure~1, selected_data)
m = fit.variogram(v, vgm("Sph"))
plot(v, model = m)
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-88-1.pdf)<!-- --> 

4) Krige the data and extract the predicted (or interpolated) values.

```r
kriged_data = gstat::krige(formula = measure~1, selected_data, grd, model=m)
```

```
## [using ordinary kriging]
```

```r
finished_data = kriged_data[1]
```

5) Create the map of soil Ph values!

```r
m <- finished_data %>%
  leaflet() %>%
  addTiles() %>%
  addStarsImage(opacity = 0.5,
                colors="RdYlGn")

saveWidget(m, "temp.html", selfcontained = FALSE)
webshot("temp.html", cliprect = "viewport")
```

![](12-Spatial-Statistics_files/figure-latex/unnamed-chunk-90-1.png)<!-- --> 

