# Introducing the R 'gimms' package
#### [Florian Detsch](http://umweltinformatik-marburg.de/en/staff/florian-detsch/) (florian.detsch(at)staff.uni-marburg.de)

The R **gimms** package provides a set of functions to 

* retrieve information about GIMMS NDVI<sub>3g</sub> files currently available online; 
* download and re-arrange the bi-monthly datasets according to date; 
* import downloaded files from native binary (ENVI) format directly into R based on the widely applied **raster** package; 
* and calculate monthly value composites (e.g., maximum value composites, MVC) from the bi-monthly input data.
    
This comprehensive tutorial introduces the package and shows a set of application examples.