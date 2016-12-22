
# Rasterize data

### Rearrange files
When working with local NDVI3g.v0 data outside the **gimms** environment, sorting files by date &mdash; *e.g.*, in preparation for stacking continuous observations, calculating monthly composite layers and so forth &mdash; is not quite straightforward. Therefore, a function to manually reorder these files might come in handy every now and then. 

Let's make this more clear: suppose you have some NDVI3g.v0 files from January to December 2000 in your current working directory. In order to import these back into R, you would probably try out something involving `list.files` at first:


```r
gimms_files_v0 <- list.files(pattern = "^geo00.*VI3g$")
head(gimms_files_v0, 8)
```


```
[1] "geo00apr15a.n14-VI3g" "geo00apr15b.n14-VI3g" "geo00aug15a.n14-VI3g"
[4] "geo00aug15b.n14-VI3g" "geo00dec15a.n16-VI3g" "geo00dec15b.n16-VI3g"
[7] "geo00feb15a.n14-VI3g" "geo00feb15b.n14-VI3g"
```

See the problem? April comes first, then August, followed by December, and so on, which is of course not very desirable. As a solution, `rearrangeFiles` allows to easily rearrange NDVI3g.v0 files either stored in a vector or located in a particular folder. The function thereby works in two different ways, either with a 'character' vector of filenames passed to `x`, or with `list.files`-style pattern matching.  


```r
gimms_files_v0 <- rearrangeFiles(pattern = "^geo00.*VI3g$", full.names = TRUE)
basename(gimms_files_v0)[1:8]
```


```
[1] "geo00jan15a.n14-VI3g" "geo00jan15b.n14-VI3g" "geo00feb15a.n14-VI3g"
[4] "geo00feb15b.n14-VI3g" "geo00mar15a.n14-VI3g" "geo00mar15b.n14-VI3g"
[7] "geo00apr15a.n14-VI3g" "geo00apr15b.n14-VI3g"
```

Note that such a manual approach is, for reasons of convenience, not required when working with `downloadGimms(..., version = 0)` or `updateInventory(version = 0)` that return sorted vectors of NDVI3g.v0 files by default.

<hr>

### Rasterize downloaded data
`rasterizeGimms` is possibly the core part of the **gimms** package as it transforms the downloaded GIMMS files from NetCDF (NDVI3g.v1) or native binary format (NDVI3g.v0) to objects of class 'Raster\*' which is &mdash; at least in my opinion &mdash; much easier to handle than ENVI files or 'ncdf4' objects. The function works with single and multiple files passed to `x` and typically returns a single 'RasterStack' objects. As an alternative, if `split = TRUE`, a 'list' of 'RasterStack' objects is returned with each entry corresponding to a single file in `x`. 

Since **gimms** v1.0.0, scaling and the rejection of 'mask-water' and 'mask-nodata' values (see also the [official README](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/00READMEgeo.txt)) is mandatory. Furthermore, if a user-defined spatial extent is provided, `raster::crop` is switched on automatically which significantly reduces computation times. Taking the above set of NDVI3g.v0 files (January to December 2000) as input vector, the function call looks as follows:


```r
## rasterize ndvi3g.v0 files
gimms_raster_v0 <- rasterizeGimms(gimms_files_v0)
gimms_raster_v0[[1:2]] # January 2000
```


```
class       : RasterStack 
dimensions  : 2160, 4320, 9331200, 2  (nrow, ncol, ncell, nlayers)
resolution  : 0.08333333, 0.08333333  (x, y)
extent      : -180, 180, -90, 90  (xmin, xmax, ymin, ymax)
coord. ref. : +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 
names       : geo00jan15a.n14.VI3g, geo00jan15b.n14.VI3g 
min values  :                 -0.2,                 -0.2 
max values  :                    1,                    1 
```

The above operation should be computationally feasible on most machines. When working with longer NDVI3g time series, however, it is strongly recommended to make use of `filename` and `cores` to ensure a more memory and time efficient processing, respectively. Especially the newly introduced NetCDF files (average size: 448.0 mb) require a rather large amount of memory and, without writing the resulting images to disk, will sooner or later result in the shutdown of R.

An additional novelty in `rasterizeGimms` is the provision of simple QA features. If `keep` is specified, quality control is enabled automatically and all pixels with a flag value outside the defined value range are set to `NA` (see also Tables 1 and 2 on the next page). The above example, but this time using data from NDVI3g.v1 and discarding all pixels with a flag value of '1' (spline interpolation) or '2' (possible snow/cloud cover), goes like this:


```r
## download ndvi3g.v1 files for the year 2000
gimms_files_v1 <- downloadGimms(x = as.Date("2000-01-01"), 
                                y = as.Date("2000-12-31"))

## rasterize ndvi3g.v1 files
gimms_raster_v1 <- rasterizeGimms(gimms_files_v1, keep = 0)
gimms_raster_v1[[1:2]] # January 2000
```


```
class       : RasterStack 
dimensions  : 2160, 4320, 9331200, 2  (nrow, ncol, ncell, nlayers)
resolution  : 0.08333333, 0.08333333  (x, y)
extent      : -180, 180, -90, 90  (xmin, xmax, ymin, ymax)
coord. ref. : +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 
names       : ndvi3g_geo_v1_2000_0106.1, ndvi3g_geo_v1_2000_0106.2 
min values  :                   -0.2999,                   -0.2999 
max values  :                    0.9975,                    0.9969 
```

With a little bit of effort and the help from **RColorBrewer** (Neuwirth, 2014) and `spplot` (Pebesma and Bivand, 2005; Bivand, Pebesma, and Gomez-Rubio, 2013), it is now easy to check whether everything worked out fine:


```r
library(RColorBrewer)
library(rworldmap)

spplot(gimms_raster_v1[[7:12]], at = seq(-0.3, 1, 0.01), 
       scales = list(draw = TRUE), main = list("NDVI", cex = .9),
       colorkey = list(space = "top", width = .8, height = .7),
       col.regions = colorRampPalette(brewer.pal(11, "BrBG")), 
       sp.layout = list("sp.polygons", countriesCoarse, col = "grey"), 
       par.strip.text = list(cex = .7))
```

<center>
  <img src="http://i.imgur.com/zc2no2c.png" alt="ndvi" style="width: 800px;"/>
  <br><b>Figure 1.</b> Quality-controlled global half-monthly GIMMS NDVI3g.v1 from April to June 2000.
</center>
