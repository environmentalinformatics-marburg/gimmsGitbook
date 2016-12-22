
# What's the story?

### Background
I've been collecting functions related to the download and processing of AVHRR GIMMS data for quite some time and with the most recent update to NDVI3g (Pinzon and Tucker, 2014), it was probably a good time to stuff the most fundamental work steps into a proper R package. In this context, 'most fundamental' refers to certain operations which I tended to repeat over and over again in the context of GIMMS data processing, including

* list all half-monthly files available online at [NASA ECOCAST](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/);
* download selected (if not all) files; 
* if dealing with former product versions, re-arrange downloaded files according to date; 
* import ENVI binary (version 0, henceforth "NDVI3g.v0") or NetCDF data (version 1, henceforth "NDVI3g.v1") into R as proper **raster** objects (Hijmans, 2016); and 
* aggregate the half-monthly datasets to monthly maximum value composites (MVC).

In the following, you will find a short introduction of what I came up with so far. Feel free to contact me directly via <florian.detsch(at)staff.uni-marburg.de> or raise issues and provide (constructive) criticism on [GitHub](https://github.com/environmentalinformatics-marburg/gimms). Any suggestions on how to improve the **gimms** package are highly appreciated!

### How to install
The **gimms** package (currently version 1.0.0) is available from [CRAN](https://CRAN.R-project.org/package=gimms) and can be installed directly via 


```r
## install latest stable package release
install.packages("gimms")
library(gimms)
```

The development version including latest functionality updates, bug-fixes etc. can be installed from GitHub (Wickham and Chang, 2016). Make sure to check out the [introductory page](https://github.com/environmentalinformatics-marburg/gimms) for the latest news and updates!


```r
## install package development version
library(devtools)
install_github("environmentalinformatics-marburg/gimms", ref = "develop")
```
