
# Data download

### List available files
First of all, it is of course helpful to know which half-monthly files are currently available at [ECOCAST](http://ecocast.arc.nasa.gov/data/pub/gimms/) or [NASANEX](https://nasanex.s3.amazonaws.com/). For this purpose, `updateInventory` imports the online file inventory directly into R and, when dealing with NDVI3g.v0 data, automatically sorts the resultant file vector by date (which is not quite intuitive when dealing with names like 'geo83sep15a.n07-VI3g'). If there is no active internet connection, `updateInventory` uses the latest offline version of the file inventory instead which comes with the package and is updated regularly.

##### NDVI3g.v1 from ECOCAST

* Pinzon and Tucker (2016)
* Half-yearly NetCDF (.nc4) files containing 12 half-monthly layers
* Currently available until end 2015


```r
## ecocast, ndvi3g.v1
gimms_files_v1 <- updateInventory()
tail(gimms_files_v1, 4)
```

```
[1] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v1/ndvi3g_geo_v1_2014_0106.nc4"
[2] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v1/ndvi3g_geo_v1_2014_0712.nc4"
[3] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v1/ndvi3g_geo_v1_2015_0106.nc4"
[4] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v1/ndvi3g_geo_v1_2015_0712.nc4"
```

##### NDVI3g.v0 

**... from ECOCAST**

* Pinzon and Tucker (2014)
* Half-monthly ENVI binary files 
* Currently available until end 2013


```r
## ecocast, ndvi3g.v0 (raw binary, until end 2013)
gimms_files_v0_1 <- updateInventory(version = 0)
tail(gimms_files_v0_1, 4)
```

```
[1] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/2013s_new/geo13nov15a.n19-VI3g"
[2] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/2013s_new/geo13nov15b.n19-VI3g"
[3] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/2013s_new/geo13dec15a.n19-VI3g"
[4] "https://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/2013s_new/geo13dec15b.n19-VI3g"
```

**... from NASANEX**

* Half-monthly ENVI raw binary files 
* Currently available until end 2012


```r
## nasanex, ndvi3g.v0 (raw binary, until end 2012)
gimms_files_v0_2 <- updateInventory(server = "nasanex")
tail(gimms_files_v0_2, 4)
```

```
[1] "AVHRR/GIMMS/3G/2010s/geo12nov15a.n19-VI3g"
[2] "AVHRR/GIMMS/3G/2010s/geo12nov15b.n19-VI3g"
[3] "AVHRR/GIMMS/3G/2010s/geo12dec15a.n19-VI3g"
[4] "AVHRR/GIMMS/3G/2010s/geo12dec15b.n19-VI3g"
```

<hr>

### Download files
With the information about files currently hosted on the remote server at hand, the next logical step of the **gimms** processing chain is to download selected (if not all) half-monthly datasets. This can be achieved through `downloadGimms`, and since quite some different input types are supported, some detailed information is possibly helpful to explain the function's proper use.

##### 'missing' input = download all available files
Specifying no particular input is possibly the most straightforward way of data acquisition. The function will automatically start to download all available files for the specified product `version` (defaults to `1L`) and save them in `dsn` (defaults to `getwd()`). Setting `overwrite = TRUE` (<u>not</u> the default) tells R to overwrite all identically named files in `dsn`. Note, however, that such a behavior is not particularly desirable in the majority of cases.


```r
## ndvi3g.v1
downloadGimms()

## ndvi3g.v0
downloadGimms(version = 0)
```

##### 'numeric' input = download files for particular years
It is also possibly to specify a start year `x` and/or end year `y` to restrict the download period to certain, but continuous years of interest. If `x` (or `y`) is missing, data download will automatically start from the first (or finish with the last) file available. 


```r
## ndvi3g.v1, 2000 to 2002
downloadGimms(x = 2000, y = 2002)

## ndvi3g.v0, 2010 to last file available
downloadGimms(x = 2010, version = 0)
```

##### 'Date' input = download files for particular date range
Quite similar to the above 'numeric' method, 'Date' input is also supported which grants a finer control over the desired date range for file download. Again, if the start date `x` (or end date `y`) is missing, data download starts from the first (or finishes with the last) file available.


```r
## ndvi3g.v1, january to september 2000
downloadGimms(x = as.Date("2000-01-01"), y = as.Date("2000-09-30"))

## ndvi3g.v0, july 2010 to last file available
downloadGimms(x = as.Date("2010-07-01"), version = 0)
```

##### 'character' input = download particular files
As a fourth and final possibility, the 'character' method takes an input vector of valid online filepaths (typically returned from `updateInventory` as shown above), thus allowing to select the files to be downloaded *ad libitum*. 


```r
## ndvi3g.v1, entire period, but only the second half of each year
gimms_files_v1 <- updateInventory()
gimms_files_v1 <- gimms_files_v1[grep("0712", gimms_files_v1)]

downloadGimms(x = gimms_files_v1)

## ndvi3g.v0, entire period, but january only
gimms_files_v0 <- updateInventory(version = 0)
gimms_files_v0 <- gimms_files_v0[grep("jan", gimms_files_v0)]

downloadGimms(x = gimms_files_v0)
```
