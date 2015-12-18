
# 'gimms' goes parallel

At this point, it is time for some considerations on code performance. With the most recent update to version 0.4.0, certain **gimms** functions support parallel computation, meaning that the code can be expected to perform considerably faster now. I first tested this functionality using solely the built-in **parallel** package, but it has shown that certain operations, while running quite fast on Linux, took even longer to perform than their single-core counterparts on Windows. Anyway, most of the parallelized code now employs a combination of **foreach** and **doParallel** which results in reasonable speed gains. Among the functions offering a multi-core option are `downloadGimms`, `rasterizeGimms` and `monthlyComposite`. 

<center>
  <img src="https://i2.wp.com/gforge.se/wp-content/uploads/2015/02/Horse_power_smudge_9000.jpg" alt="parallel" style="width: 500px;"/><br>
  <small>Source: http://gforge.se/wp-content/uploads/2015/02/Horse_power_smudge_9000.jpg</small>
</center>

### Parallel downloads

Note that in case of `downloadGimms`, an adequately fast active internect connection is required in order to accomplish multiple high-speed downloads in parallel. For instance, if you want to retrieve all files from the year 2000, the standard approach looks as follows. 


```r
system.time(
  local_files <- downloadGimms(x = as.Date("2000-01-01"), 
                               y = as.Date("2000-12-31"), 
                               dsn = paste0(getwd(), "/data"), overwrite = TRUE)
)
#   user  system elapsed 
#  0.336   2.736  84.654 
```

And here is the parallel version which operates on 3 nodes (note the newly introduced 'cores' argument) and, at least on my machine and using my internet connection, performs roughly 2.5 times faster.


```r
system.time(
  local_files <- downloadGimms(x = as.Date("2000-01-01"), 
                               y = as.Date("2000-12-31"), 
                               dsn = paste0(getwd(), "/data"), overwrite = TRUE, 
                               cores = 3L)
  
)
#   user  system elapsed 
#  0.181   0.154  32.919 
```

### Parallel rasterization
Speed gains can furthermore be achieved when running `rasterizeGimms` with the `raster::writeRaster` option enabled, i.e., parameter `filename` specified. When run in parallel, the operation performs considerably faster as compared to the base implementation. 


```r
## first, the base version from above
system.time(
  local_rasters <- rasterizeGimms(local_files, filename = local_files, 
                                  format = "GTiff", overwrite = TRUE)
)
#   user  system elapsed 
# 62.916   4.594  70.480 

## next, the parallelized version
rasterizeGimmsParallel <- function(files, nodes = 3, ...) {
  
  # create and register parallel backend
  library(doParallel)
  cl <- makeCluster(nodes)
  registerDoParallel(cl)
  
  # loop over 'x' and process single files in parallel
  ls_rst <- foreach(i = files, .packages = "gimms", 
                    .export = ls(envir = globalenv())) %dopar% {
                      rasterizeGimms(i, filename = i, ...)
                    }
  
  # stack layers
  rst <- raster::stack(ls_rst)
  
  # deregister parallel backend
  stopCluster(cl)
  
  # return layers
  return(rst)
}

system.time(
  rasterizeGimmsParallel(local_files, 
                         format = "GTiff", overwrite = TRUE)
)
#   user  system elapsed 
#  0.122   0.105  37.298
```

In the context of parallel processing, feel free to also browse the compendium of advanced applications based on GIMMS NDVI<sub>3g</sub> data herein after. There are some more examples included demonstrating the reasonable use of **doParallel** functionality (RevolutionAnalytics and Weston, 2015) along with the **gimms** package.
