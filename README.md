# Introducing the R 'gimms' package

<div align="left">Author</div> | <div align="left">Florian Detsch</div>
--------------- | -----------    
E-Mail | [florian.detsch@staff.uni-marburg.de](mailto:florian.detsch@staff.uni-marburg.de)
Website | http://www.uni-marburg.de/fb19/fachgebiete/umweltinformatik/detschf/index.html 

<hr>

The R **gimms** package provides a set of functions to 

* retrieve information about AVHRR GIMMS NDVI3g files currently available online; 
* download (and re-arrange, in the case of older product versions) the half-monthly datasets; 
* import downloaded files from native binary (ENVI) or NetCDF (.nc4) format directly into R based on the popular **raster** package; 
* perform comprehensive quality control based on the companion quality flags;
* calculate monthly value composites (*e.g.*, using the maximum value) from the half-monthly input data;
* and derive long-term monotonic trends from the Mann-Kendall test, optionally featuring pre-whitening to account for lag-1 autocorrelation.
    
This comprehensive tutorial introduces the package and includes a selection of application examples. For latest functionality updates and bug-fixes, please refer to the official **gimms** [GitHub repository](https://github.com/environmentalinformatics-marburg/gimms) maintained by the [Environmental Informatics working group](http://umweltinformatik-marburg.de/en/environmenltalinformatics-marburg/) at Philipps-Universität Marburg, Germany.