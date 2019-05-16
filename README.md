# ogc-polygon-subset
proposed strategies for implementing polygon subsetting across ORNL DAAC OGC services             

Launch a live version of the demo notebook by clicking this button:
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/jjmcnelis/ogc-polygon-subset/master)

The notebook demonstrates our take on the most straightforward path to providing polygon subsetting capabilities for ORNL DAAC datasets accessible through OGC services. 

**Why we should use GDAL/OGR:**
* access to full capabilities of GDAL/OGR for in-memory raster and vector processing         
* python's GDAL/OGR wrapper is a low-level interface and it performs well even compared to compiled code. Combined with C-optimized `numpy`, we can develop production code that's flexible and easy to maintain for other high level services beyond polygon subsetting.       
* stable code        

**Disadvantages to GDAL/OGR:**             
* basic GIS operations require more steps that you would expect. See this snip from the OPeNDAP example in the notebook:
```{python}
memvector = ogr.GetDriverByName('Memory').CreateDataSource('mem')  # open OGR vector memory dataset
memlyr = memvector.CreateLayer('poly', srs=target_srs)             # make a dummy layer in daymet lcc
memfeat = ogr.Feature(memlyr.GetLayerDefn())                       # init a dummy feature
memfeat.SetGeometryDirectly(geom)                                  # set transformed geom to feature
memlyr.CreateFeature(memfeat)                                      # add dummy feature to layer
```
These fives lines only do one thing: make a new ogr vector dataset to store the transformed geometry from the input shapfile. 

Go look at the notebook now.
