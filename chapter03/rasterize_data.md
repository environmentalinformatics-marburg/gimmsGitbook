
# Rasterize data

### Rearrange files
As mentioned earlier, it is possible to have `updateInventory` return a vector of filenames sorted by date in ascending order. Sorting the files surely makes sense when it comes to `raster::stack`-ing continuous observations, calculating monthly composite layers and so forth, but is not necessarily required at the initial stage. `updateInventory(sort = TRUE)` (the default setting) is merely a wrapper around `rearrangeFiles` which may as well be executed as a stand-alone version later on. 

`rearrangeFiles` works in two different ways, either with a 'character' vector of (local or online) filenames passed on to `x` or with `list.files`-style pattern matching. While the former approach is quite straightforward, the latter requires the function to search the folder `dsn` for previously downloaded files matching a particular `pattern` (typically starting with the default setting "^geo"). Importing a sorted vector of already downloaded files from 2013, for instance, would work as follows.


```r
rearrangeFiles(dsn = paste0(getwd(), "/data"), 
               pattern = "^geo13")
```


```
 [1] "geo13jan15a.n19-VI3g" "geo13jan15b.n19-VI3g" "geo13feb15a.n19-VI3g"
 [4] "geo13feb15b.n19-VI3g" "geo13mar15a.n19-VI3g" "geo13mar15b.n19-VI3g"
 [7] "geo13apr15a.n19-VI3g" "geo13apr15b.n19-VI3g" "geo13may15a.n19-VI3g"
[10] "geo13may15b.n19-VI3g" "geo13jun15a.n19-VI3g" "geo13jun15b.n19-VI3g"
[13] "geo13jul15a.n19-VI3g" "geo13jul15b.n19-VI3g" "geo13aug15a.n19-VI3g"
[16] "geo13aug15b.n19-VI3g" "geo13sep15a.n19-VI3g" "geo13sep15b.n19-VI3g"
[19] "geo13oct15a.n19-VI3g" "geo13oct15b.n19-VI3g" "geo13nov15a.n19-VI3g"
[22] "geo13nov15b.n19-VI3g" "geo13dec15a.n19-VI3g" "geo13dec15b.n19-VI3g"
```

### Rasterize downloaded data
`rasterizeGimms` is possibly the core part of the **gimms** package as it transforms the downloaded GIMMS files from native binary format into objects of class 'Raster*' which is much easier to handle as compared to ENVI files. The function works with both single and multiple files passed on to `x` and, in the case of the latter, returns a 'RasterStack' rather than a single 'RasterLayer'. It is up to the user to decide whether or not to discard 'mask-water' values (-10,000) and 'mask-nodata' values (-5,000) (see also the [official README](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/00READMEgeo.txt)). Also, the application of the scaling factor (1/10,000) is not mandatory. Taking the above set of `gimms_files` (Jul-Dec 1981) as input vector (notice the `full.names` argument passed on to `list.files` in the example below), the function call looks as follows.


```r
## list available files
gimms_files <- rearrangeFiles(dsn = paste0(getwd(), "/data"), 
                              pattern = "^geo81", full.names = TRUE)

## rasterize files
gimms_raster <- rasterizeGimms(gimms_files, 
                               filename = gimms_files, 
                               format = "GTiff", overwrite = TRUE)
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
In order to import the GIMMS binary files into R via `raster::raster`, the creation of header files (.hdr) that are located in the same folder as the binary files staged for processing is mandatory. The standard files required to properly process GIMMS NDVI<sub>3g</sub> data are created via `createHeader` and typically include the following parameters. 


```r
## create gimms ndvi3g standard header file
gimms_header <- createHeader("~/geo13jul15a.n19-VI3g")

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
[1] "byte order = 1"
```

It is possible to automatically remove the created header files once all operations have finished by setting `rasterizeGimms(..., remove_header = TRUE)`. Although `rasterizeGimms` automatically invokes `createHeader`, the function also runs as stand-alone version.


