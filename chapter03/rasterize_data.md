
# Rasterize data

### Rearrange files
As mentioned earlier, it is possible to have `updateInventory` return a vector of filenames sorted by date in ascending order. Sorting the files surely makes sense when it comes to `raster::stack`-ing continuous observations, calculating monthly composite layers and so forth, but is not necessarily required at the initial stage. `updateInventory(sort = TRUE)` (the default setting) is merely a wrapper around `rearrangeFiles` which may as well be executed as a stand-alone version later on. 

`rearrangeFiles` works in two different ways, either with a 'character' vector of (local or online) filenames passed on to `x` or with `list.files`-style pattern matching. While the former approach is quite straightforward, the latter requires the function to search the folder `dsn` for previously downloaded files matching a particular `pattern` (typically starting with the default setting "^geo"). Importing a sorted vector of already downloaded files from Jul-Dec 1981, for instance, would work as follows.


```r
gimms_files <- rearrangeFiles(dsn = paste0(getwd(), "/data"), 
                              pattern = "^geo81.*VI3g$", 
                              full.names = TRUE)

basename(gimms_files)
```


```
 [1] "geo81jul15a.n07-VI3g" "geo81jul15b.n07-VI3g" "geo81aug15a.n07-VI3g"
 [4] "geo81aug15b.n07-VI3g" "geo81sep15a.n07-VI3g" "geo81sep15b.n07-VI3g"
 [7] "geo81oct15a.n07-VI3g" "geo81oct15b.n07-VI3g" "geo81nov15a.n07-VI3g"
[10] "geo81nov15b.n07-VI3g" "geo81dec15a.n07-VI3g" "geo81dec15b.n07-VI3g"
```

### Rasterize downloaded data
`rasterizeGimms` is possibly the core part of the **gimms** package as it transforms the downloaded GIMMS files from native binary format into objects of class 'Raster*' which is much easier to handle as compared to ENVI files. The function works with both single and multiple files passed on to `x` and, in the case of the latter, returns a 'RasterStack' rather than a single 'RasterLayer'. It is up to the user to decide whether or not to discard 'mask-water' values (-10,000) and 'mask-nodata' values (-5,000) (see also the [official README](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/00READMEgeo.txt)). Also, the application of the scaling factor (1/10,000) is not mandatory. Taking the above set of `gimms_files` (Jul-Dec 1981) as input vector (notice the `full.names` argument passed on to `list.files` in the example above), the function call looks as follows.


```r
## rasterize files
gimms_raster <- rasterizeGimms(gimms_files, 
                               filename = gimms_files, 
                               format = "GTiff", overwrite = TRUE)
```


```
Error in compareRaster(rasters): different extent
```

Since this operation usually takes some time, we highly recommend to make use of the `filename` argument that automatically invokes `raster::writeRaster`. With a little bit of effort and the help of **RColorBrewer** (Neuwirth, 2014) and **spplot** (Pebesma and Bivand, 2005; Bivand, Pebesma, and Gomez-Rubio, 2013), it is now easy to check whether everything worked out fine.


```r
## adjust layer names
names(gimms_raster) <- basename(gimms_files)

## visualize single layers
library(RColorBrewer)
spplot(gimms_raster, 
       at = seq(-0.2, 1, 0.1), 
       scales = list(draw = TRUE), 
       col.regions = colorRampPalette(brewer.pal(11, "BrBG")))
```

<center>
  <img src="http://i.imgur.com/Qr3FuNr.png" alt="spplot" style="width: 800px;"/>
  <br><br><b>Figure 1.</b> Global bi-monthly GIMMS NDVI<sub>3g</sub> images from July to December 2013.
</center>

### Create a header file
In order to import the GIMMS binary files into R via `raster::raster`, the creation of header files (.hdr) that are located in the same folder as the binary files staged for processing is mandatory. The standard files required to properly process GIMMS NDVI<sub>3g</sub> data are created via the non-exported `createHeader` function and typically include the following parameters. 


```r
## create gimms ndvi3g standard header file
gimms_header <- gimms:::createHeader(paste0(getwd(), "/geo13jul15a.n19-VI3g"))
readLines(gimms_header)
```


```
[1] "ENVI"
[1] "description = { R-language data }"
[1] "samples = 2160"
[1] "lines = 4320"
[1] "bands = 1"
[1] "data type = 2"
[1] "header offset = 0"
[1] "interleave = bsq"
[1] "sensor type = AVHRR"
[1] "byte order = 1"
```

The function is not explicitly exported since `rasterizeGimms` automatically creates the header files corresponding to each of the specified input files. Hence, you shouldn't be required to run the standalone version of the function unless you are interested in it. Note that it is also possible to automatically remove all the header files upon process completion by setting `rasterizeGimms(..., remove_header = TRUE)`. 
