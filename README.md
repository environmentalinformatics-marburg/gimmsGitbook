# Introducing the R 'gimms' package

Author          | <div align="left">Florian Detsch</div>
--------------- | -----------    
E-Mail | [florian.detsch@staff.uni-marburg.de](mailto:florian.detsch@staff.uni-marburg.de)
Website | http://umweltinformatik-marburg.de/en/staff/florian-detsch/ 

<hr>

The R **gimms** package provides a set of functions to 

* retrieve information about GIMMS NDVI<sub>3g</sub> files currently available online; 
* download and re-arrange the bi-monthly datasets according to date; 
* import downloaded files from native binary (ENVI) format directly into R based on the widely applied **raster** package; 
* calculate monthly value composites (e.g., maximum value composites, MVC) from the bi-monthly input data;
* and derive long-term monotonous trends from the Mann-Kendall trend test, optionally featuring pre-whitening to account for lag-1 autocorrelation.
    
This comprehensive tutorial introduces the package and includes a selection of application examples. For latest functionality updates and bug-fixes, please refer to the official **gimms** [GitHub repository](https://github.com/environmentalinformatics-marburg/gimms) maintained by the [Environmental Informatics working group](http://umweltinformatik-marburg.de/en/environmenltalinformatics-marburg/), Philipps-Universität Marburg, Germany.