
# Data download

### List available files
For any subsequent processing steps, it is helpful to know which bi-monthly files are currently available online at [ECOCAST]((http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/). `updateInventory` has been designed just for that purpose as it imports the file inventory stored online in ['00FILE-LIST.txt'](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/00FILE-LIST.txt) directly into R. By setting `sort = TRUE` (default), it is possible to return the output vector sorted by date which is anything but intuitive when dealing with naming conventions in the form of 'geo83sep15a.n07-VI3g'. If there is no active internet connection available, `updateInventory` automatically imports the latest offline version of the file inventory which is stored (and regularly updated) in `system.file("extdata", "inventory.rds", package = "gimms")`.


```r
gimms_files <- updateInventory()
head(gimms_files)
```

```
[1] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81jul15a.n07-VI3g"
[2] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81jul15b.n07-VI3g"
[3] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81aug15a.n07-VI3g"
[4] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81aug15b.n07-VI3g"
[5] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81sep15a.n07-VI3g"
[6] "http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/1980s_new/geo81sep15b.n07-VI3g"
```

### Download files
With the information about files currently hosted on the remote server at hand, the next logical step of the **gimms** processing chain is to download selected (if not all) bi-monthly datasets. This can be achieved by running `downloadGimms`, but since the function works with different types of input parameters (if any), some *ex ante* information is possibly helpful to explain the function's proper use.

##### 'missing' input = download entire collection
Specifying no particular input is possibly the most straightforward way of data acquisition. The function will automatically start to download the entire collection of files (currently July 1981 to December 2013) and store the data in `dsn`. It is up to the user's judgement to set `overwrite = TRUE` which would tell R to overwrite previously downloaded data located in `dsn`. In most cases, however, such a behavior is not particularly desirable, and instead setting `overwrite = FALSE` would simply tell R to skip the currently processed file.


```r
## download entire gimms collection
downloadGimms(dsn = paste0(getwd(), "/data"))
```

##### 'numeric' input = download temporal range by years
It is also possibly to specify a start year (`x`) and/or end year (`y`) to limit the temporal coverage of the datasets to be downloaded. In case `x` (or `y`) is missing, data download will automatically start from the first (or finish with the last) year available. 


```r
## download gimms data from 1998-2000
downloadGimms(x = 1998, y = 2000, 
              dsn = paste0(getwd(), "/data"))
```

##### 'Date' input = download temporal range by dates
Similar to 'numeric' input, the function also operates with 'Date' input to grant the user a finer control over temporal coverage (starting from package version 0.3.0 onward). Again, if `x` (or `y`) is missing, data download starts with the oldest (finishes with the latest) file available.


```r
## download gimms data from the first half of 2000 only
downloadGimms(x = as.Date("2000-01-01"), y = as.Date("2000-06-30"), 
              dsn = paste0(getwd(), "/data"))
```

##### 'character' input = download particular files
As a fourth and final possibility to run `downloadGimms`, it is also possible to supply a 'character' vector consisting of valid online filepaths. The latter can easily be retrieved from `updateInventory` (as demonstrated above) and directly passed on to the input argument `x`. 


```r
## download manually selected files
downloadGimms(x = gimms_files[c(98:110, 132)], 
              dsn = paste0(getwd(), "/data"))
```
