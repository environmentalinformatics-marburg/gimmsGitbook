
# Monthly composites

Sometimes, it is required to calculate monthly value composites from the bi-monthly GIMMS datasets, for example if the user needs to ensure temporal overlap with some other ecological or eco-climatological time series. **gimms** features a function called `monthlyComposite` which works both on vectors of filenames and entire 'RasterStack' objects (ideally returned by `rasterizeGimms`) and calculates monthly values based on a user-defined function. For instance, `fun = max` creates monthly maximum value composites (MVC). Needless to say, the function is heavily based on `stackApply` from the fabulous **raster** package and assumes numeric vectors of monthly `indices` (or text substrings from `pos1` to `pos2` from which to deduce such indices, see `?monthlyIndices`) as input variable. The actual code work is relatively straightforward.


```r
## .tif files created during the previous step
gimms_files_tif <- rearrangeFiles(dsn = paste0(getwd(), "/data"), 
                                  pattern = "^geo81.*.tif$", full.names = TRUE)

## create monthly maximum value composites
gimms_raster_mvc <- monthlyComposite(gimms_files_tif)
```

Note that in the above code chunk, `rearrangeFiles` is used to re-arrange previously created GeoTIFF files according to date. Again and this time with a little help from **reshape2** (Wickham, 2007) and **ggplot2** (Wickham, 2009), the effects from `monthlyComposite` can easily be seen. Displayed below are the densityplots of all NDVI values during the 1st half of July 1981 (green), during the 2nd half of July 1981 (turquoise) and the resulting MVC values (black).


```r
## concatenate data
val <- data.frame("ndvi_15a" = na.omit(getValues(gimms_raster[[1]])), 
                  "ndvi_15b" = na.omit(getValues(gimms_raster[[2]])), 
                  "ndvi_mvc" = na.omit(getValues(gimms_raster_mvc[[1]])))

## wide to long format
library(reshape2)
val_mlt <- melt(val)

## colors
library(devtools)
install_github("environmentalinformatics-marburg/Rsenal")

library(Rsenal)
cols <- envinmrPalette(5)[c(3, 2, 5)]
names(cols) <- levels(val_mlt$variable)

## linetypes
ltys <- c("solid", "solid", "longdash")
names(ltys) <- levels(val_mlt$variable)

## build ggplot
library(ggplot2)
ggplot(aes(x = value, group = variable, colour = variable, 
           linetype = variable), data = val_mlt) + 
  geom_hline(yintercept = 0, size = .5, colour = "grey65", linetype = "dashed") +
  geom_line(stat = "density", size = 1.2) + 
  scale_colour_manual("dataset", values = cols) + 
  scale_linetype_manual("dataset", values = ltys) + 
  labs(x = "\nNDVI", y = "Density\n") + 
  theme_bw() + 
  theme(panel.grid = element_blank(), 
        legend.key.width = grid::unit(1.8, "cm"))
```



<center>
  <img src="http://i.imgur.com/wFF8Oqv.png" alt="ggplot" style="width: 650px;"/>
  <br><br><b>Figure 2.</b> Kernel density distribution of GIMMS NDVI<sub>3g</sub> values during the first (green) and second half of July 1981 (turquoise) and resulting value distribution of the maximum value composite layer (MVC; black). 
</center> 
