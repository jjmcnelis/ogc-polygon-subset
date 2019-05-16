# ogc-polygon-subset
proposed strategies for implementing polygon subsetting across ORNL DAAC OGC services             
*Excuse my verbosity; typing out every thought that pops in my head helps me think through these things...*

Launch a live version of the demo notebook by clicking this button:
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/jjmcnelis/ogc-polygon-subset/master)

The notebook demonstrates our take on the most straightforward path to providing polygon subsetting capabilities for all of ORNL DAAC's datasets accessible through OGC services. The example in the notebook simplified further (though they're about as simple in their implementation as is feasible IMO) by placing a few reasonable constraints on the user, and by using packages that provide a few more conveniences than the bare bones wrapper for the GDAL/OGR API. 

**Why we should use GDAL/OGR:**
* access to full capabilities of GDAL/OGR for in-memory raster and vector processing
* python's GDAL/OGR wrapper is a low-level interface
And it performs well even compared to compiled code. Combined with the C-optimized array processing capabilities of `numpy`, we can develop production-ready code that's flexible and easy to maintain for other high level services beyond polygon subsetting. I'm even told (through discussions with folks on geo message boards; no way to verify this) that the python methodology for standard types of GIS operations resembles quite a bit what the code would look like written in C.
* code will be stable
**Disadvantages to GDAL/OGR:**
* fundamental GIS operations require more steps that you would expect. See this snip from cell block five of the notebook:
```{python}
memvector = ogr.GetDriverByName('Memory').CreateDataSource('mem')  # open OGR vector memory dataset
memlyr = memvector.CreateLayer('poly', srs=target_srs)             # make a dummy layer in daymet lcc
memfeat = ogr.Feature(memlyr.GetLayerDefn())                       # init a dummy feature
memfeat.SetGeometryDirectly(geom)                                  # set transformed geom to feature
memlyr.CreateFeature(memfeat)                                      # add dummy feature to layer
```
These fives lines really only do one thing, grab the polygon geometry from the input shapefile. Shapefile format is just a little bit too "fully featured" to be convenient to access through the barebones OGR tools. Just my thoughts on it.

Go look at the notebook now.
