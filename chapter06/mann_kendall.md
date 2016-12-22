
# Global Mann-Kendall trend test

The function `significantTau` has been developed and added to **gimms** to facilitate the calculation of reliable long-term monotonous trends. It is up to the user to decide whether or not to apply pre-whitening prior to the Mann-Kendall trend test in order to account for lag-1 autocorrelation. Currently, the function supports the pre-whitening algorithms proposed by Yue, Pilon, Phinney, et al. (2002) and Zhang, Vincent, Hogg, et al. (2000) which are both included in the **zyp** package (Bronaugh and Werner, 2013). If no pre-whitening is desired, `significantTau` is merely a wrapper function around `MannKendall` from **Kendall** (McLeod, 2011).


```r
### download data -----

## number of cores for parallel processing
cores <- parallel::detectCores() - 1

## download entire gimms ndvi3g collection in parallel
gimms_fls <- downloadGimms(x = as.Date("1982-01-01"), cores = cores)


### rasterize images including quality control -----

## reference extent
library(rworldmap)
deu <- subset(countriesCoarse, ADMIN == "Germany")

## rasterize
gimms_rst <- rasterizeGimms(gimms_fls, ext = deu, keep = 0, cores = cores,
                            filename = gsub(".nc4", ".tif", gimms_files),
                            format = "GTiff", overwrite = TRUE)


### remove seasonal signal -----

library(remote)
gimms_dsn <- deseason(gimms_rst, cycle.window = 24L)


### mann-kendall trend test (p < 0.001) -----

gimms_mk <- significantTau(gimms_dsn, prewhitening = TRUE)
gimms_mk
```



<center>
  <img src="http://i.imgur.com/IYJyJP3.png" alt="trends" style="width: 600px;"/><br>
  <b>Figure 3.</b> Long-term Mann-Kendall trends (1982 to 2015; <i>p < 0.001</i>) over Germany derived from GIMMS NDVI3g.v1.
</center>
