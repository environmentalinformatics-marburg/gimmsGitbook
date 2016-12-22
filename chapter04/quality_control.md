

# Quality control

No matter which QA approach (if any) you choose to get rid of non-reliable NDVI3g values, please be aware that flags have quite a different meaning depending on the product version under consideration. Values of '1' and '2', which testify good data quality in NDVI3g.v0 (Table 1), refer to spline-interpolated and snow or cloud-covered targets in NDVI3g.v1, respectively (Table 2).  
<br>
<center><small><b>Table 1.</b> NDVI3g.v0 flag values and their meanings.</small></center>

| **Flag value** | **Description** |
|:----------:|-------------|
|  1 and 2   |  good value |
|     3      | value derived from spline interpolation   |
|     4      | value derived from spline interpolation, possibly snow |
|     5      | value derived from average seasonal profile |
|     6      | value derived from average seasonal profile, possibly snow |
|     7      | missing value |

<br>
<center><small><b>Table 2.</b> NDVI3g.v1 flag values and their meanings.</small></center>

| **Flag value** | **Description** |
|:----------:|-------------|
|     0      |  from data |
|     1      | spline interpolation   |
|     2      | possible snow/cloud cover |

<br>
As said, `rasterizeGimms` already offers basic QA functionality through the argument `keep`, so there is actually no need to do this separately. For reasons of convenience and in order to ensure compatibility with older package versions, however, NDVI3g quality control is also available through the function `qualityControl` which is, at least at the moment, compatible with 2-layered 'RasterStack' objects (NDVI and flags) only. Here's how this seperate call would look like for the scene from the first half of April 2000: 


```r
## get unchecked ndvi
ndvi <- rasterizeGimms(gimms_files_v1[1], cores = parallel::detectCores() - 1)

## extract and calculate flags (see also ncdf4::nc_open(gimms_files_v1[1]))
flag <- raster::stack(gimms_files_v1[1], varname = "percentile")
flag <- floor(flag / 2000)

## perform quality control
apr00 <- stack(ndvi[[7]], flag[[7]]) # first half of april 2000
qualityControl(apr00, keep = 0)
```

And here's the referring graphical output. Neat, isn't it?

<br>
<center>
  <img src="http://i.imgur.com/8mFcYc2.png" alt="qc" style="width: 700px;"/>
  <br><br><b>Figure 2.</b> a) Global GIMMS NDVI3g.v1; b) corresponding flags from the first half of April 2000 (red = '1', blue (mostly in North-West Canada) = '2'); and c) quality-controlled GIMMS NDVI3g.v1.
</center>

