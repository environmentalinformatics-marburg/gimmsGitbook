
# Monthly composites

Sometimes, it is required to calculate monthly value composites from the half-monthly GIMMS datasets, for example if the user needs to ensure temporal overlap with some other ecological or eco-climatological time series. **gimms** features a function called `monthlyComposite` which works both on vectors of filenames and 'RasterStack' objects (ideally returned by `rasterizeGimms`) and calculates monthly values based on a user-defined function. For instance, `fun = max` creates monthly maximum value composites (MVC). 

Needless to say, the function is heavily based on `stackApply` from the fabulous **raster** package and assumes numeric vectors of monthly `indices` (or text substrings from `pos1` to `pos2` from which to deduce such indices, see `?monthlyIndices`) as input variable. The actual code work is relatively straightforward.


```r
## create monthly maximum value composites
gimms_mvc_v1 <- monthlyComposite(gimms_raster_v1, 
                                 indices = monthlyIndices(gimms_files_v1))
gimms_mvc_v1[[4]] # april 2000
```


```
class       : RasterLayer 
dimensions  : 2160, 4320, 9331200  (nrow, ncol, ncell)
resolution  : 0.08333333, 0.08333333  (x, y)
extent      : -180, 180, -90, 90  (xmin, xmax, ymin, ymax)
coord. ref. : +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 
data source : in memory
names       : ndvi3g_geo_v1_2000_04 
values      : -0.2937, 0.9996  (min, max)
```

<br>
<center>
  <img src="http://i.imgur.com/k37Cnd8.png" alt="ggplot" style="width: 650px;"/>
  <br><br><b>Figure 3.</b> 1-d Kernel density distribution of GIMMS NDVI3g.v1 values during the first (green) and second half (turquoise) of April 2000 and resulting value distribution of the maximum value composite layer (MVC; black). 
</center> 
