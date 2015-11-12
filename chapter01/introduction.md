
# What's the story?

### Background
I've been collecting functions related to the download and processing of AVHRR GIMMS data for quite some time and with the most recent update to NDVI<sub>3g</sub> (Pinzon and Tucker, 2014), it was probably a good time to stuff the most fundamental work steps into a proper R package. In this context, 'most fundamental' refers to certain operations which I tended to repeat over and over again in the context of GIMMS data processing, including

* list all bi-monthly files available online at [NASA ECOCAST](http://ecocast.arc.nasa.gov/data/pub/gimms/3g.v0/),
* download selected (if not all) files, 
* re-arrange downloaded files according to date, 
* transform ENVI binary data to proper **raster** format (Hijmans, 2015) and 
* aggregate bi-monthly datasets to monthly maximum value composites (MVC).

In the following, you will find a short introduction of what I came up with so far. Feel free to contact me directly via <florian.detsch(at)staff.uni-marburg.de> or raise issues and provide (constructive) criticism on [GitHub](https://github.com/environmentalinformatics-marburg/gimms). Any suggestions on how to improve the **gimms** package are highly appreciated!

### How to install
The **gimms** package (currently version 0.2.0) is now officially on CRAN and can be install directly via 


```r
## install latest stable package release
install.packages("gimms")
```

The development version including latest functionality updates, bug-fixes etc. can be installed directly from GitHub via **devtools** (Wickham and Chang, 2015). Make sure to check out the [introductory page](https://github.com/environmentalinformatics-marburg/gimms) for the latest news and updates!


```r
## install package development version
library(devtools)
install_github("environmentalinformatics-marburg/gimms", ref = "develop")
```


