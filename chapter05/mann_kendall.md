
# Global Mann-Kendall trend test

A function called `significantTau` has recently been added to **gimms** in order to facilitate the calculation of reliable long-term monotonous trends. It is up to the user to decide whether or not to apply pre-whitening prior to applying the Mann-Kendall trend test in order to account for lag-1 autocorrelation. Currently, the function supports the pre-whitening algorithms proposed by Yue, Pilon, Phinney, et al. (2002) and Zhang, Vincent, Hogg, et al. (2000) which are both included in the **zyp** package (Bronaugh and Werner, 2013). 


```r
################################################################################
## download data
################################################################################

## download entire gimms ndvi3g collection in parallel
library(doParallel)
supcl <- makeCluster(3)
registerDoParallel(sucl)

gimms_files <- updateInventory(sort = TRUE)
gimms_files <- foreach(i = gimms_files, .packages = "gimms", 
                       .combine = "c") %dopar% downloadGimms(i, dsn = "data/")

################################################################################
## rasterize binary files
################################################################################

## rasterize gimms ndvi3g binary files in parallel (see function definition of 
## `rasterizeGimmsParallel` in the previous section)
gimms_raster <- rasterizeGimmsParallel(gimms_files, 
                                       format = "GTiff", overwrite = TRUE)

## remove incomplete first year
gimms_files <- gimms_files[-(1:12)]
gimms_raster <- gimms_raster[[-(1:12)]]

################################################################################
## resample to a lower spatial resolution (to avoid stack overflow)
################################################################################

## aggregate to a lower spatial resolution
gimms_raster_agg <- foreach(i = 1:nlayers(gimms_raster), 
                            .packages = c("raster", "rgdal")) %dopar%
  aggregate(gimms_raster[[i]], fact = 3, fun = median, 
            filename = paste0("data/agg/AGG_", names(gimms_raster[[i]])), 
            format = "GTiff", overwrite = TRUE)

gimms_raster_agg <- stack(gimms_raster_agg)

################################################################################
## remove seasonal signal
################################################################################

## calculate long-term bi-monthly means
gimms_list_means <- foreach(i = 1:24, 
                            .packages = c("raster", "rgdal")) %dopar% {
  
  # layers corresponding to current period (e.g. '82jan15a')
  id <- seq(i, nlayers(gimms_raster_agg), 24)
  gimms_raster_agg_tmp <- gimms_raster_agg[[id]]
  
  # calculate long-term mean of current period (e.g. for 1982-2013 'jan15a')
  calc(gimms_raster_agg_tmp, fun = mean, na.rm = TRUE)
} 

gimms_raster_means <- stack(gimms_list_means)

## replicate bi-monthly 'gimms_raster_means' to match up with number of layers of 
## initial 'gimms_raster_agg' (as `foreach` does not support recycling!)
gimms_list_means <- replicate(nlayers(gimms_raster_agg) / nlayers(gimms_raster_means), 
                              gimms_raster_means)
gimms_raster_means <- stack(gimms_list_means)

## subtract long-term mean from bi-monthly values
files_out <- names(gimms_raster_agg)
gimms_list_deseason <- foreach(i = 1:nlayers(gimms_raster_agg), 
                               .packages = c("raster", "rgdal")) %dopar% {
  
  rst <- gimms_raster_agg[[i]] - gimms_raster_means[[i]]
  rst <- writeRaster(rst, 
                     filename = paste0("data/dsn/DSN_", names(gimms_raster_agg[[i]])), 
                     format = "GTiff", overwrite = TRUE)
  
}

gimms_raster_deseason <- stack(gimms_list_deseason)

################################################################################
## mann-kendall trend test (p < 0.001)
################################################################################

## apply custom function on a pixel basis
gimms_raster_trend <- overlay(gimms_raster_deseason, fun = function(x) {
  gimms::significantTau(x, prewhitening = TRUE, conf.intervals = FALSE)
}, filename = "data/out/gimms_mk001_8213", 
format = "GTiff", overwrite = TRUE)

################################################################################
## visualize data
################################################################################

## complementary shapefile data
library(rworldmap)
data("countriesCoarse")

## colors, see http://colorbrewer2.org/
library(RColorBrewer)
cols <- colorRampPalette(brewer.pal(11, "BrBG"))

## create plot
spplot(gimms_raster_trend, col.regions = cols(111), scales = list(draw = TRUE), 
       sp.layout = list("sp.polygons", countriesCoarse, col = "grey75"), 
       at = seq(-.55, .55, .01))
```



<center>
  <img src="http://i.imgur.com/5JvSV42.png" alt="spplot" style="width: 800px;"/><br>
  <b>Figure 3.</b> Long-term trend (1982-2013; p < 0.001) in global GIMMS NDVI<sub>3g</sub> derived from pixel-based Mann-Kendall trend tests (Mann, 1945).
</center>
