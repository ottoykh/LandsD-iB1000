
# iB1000 Extraction 

This code/ program is to decompose the LandsD iB1000 with code-less end-product.

The following is a simple process to decompse and extraction features from iB1000,  user need to download the fullset seamless geodatabase from the LandsD hkmapservice then unzip the geoadtabase. 
## Demo
- Download the Fullset Seamless geodatabase from LandsD

```python 
 !wget "https://open.hkmapservice.gov.hk/OpenData/directDownload?productName=iB1000&sheetName=Fullset_Seamless&productFormat=FGDB" -O Fullset_Seamless_FGDB.zip
```
- Decompose the geoadtabase from zip file

```python 
 !unzip -o Fullset_Seamless_FGDB.zip
```
- Install the package related to the extraction 
```python 
import os
import geopandas as gpd
import pandas as pd
import fiona
from shapely.geometry import Point
```
- Retrieve and List the layers from the geodatabase
```python 
fgdb_path = "/content/20240804/iB1000.gdb" #Change this directory by your own local IDE 

print(f"Listing layers in {fgdb_path}:")
with fiona.Env():
    for name in fiona.listlayers(fgdb_path):
        print(name)
```
- Review the layer attributes 
```python 
with fiona.Env():
  layer = 'Site'
  with fiona.open(fgdb_path, layer=layer) as src:
    gdf = gpd.GeoDataFrame.from_features(src)
  print(f"CRS: {gdf.crs}")
  print(f"Schema: {gdf.columns.tolist()}")
  print(f"Number of features: {len(gdf)}")
```
- Extract and export the layer into GeoJSON
```python 
with fiona.Env():
   layer = 'RailwayPolygon'
   with fiona.open(fgdb_path, layer=layer) as src:
     gdf = gpd.GeoDataFrame.from_features(src)

   gdf = gdf.set_crs(epsg=2326)
   gdf.to_file("RailwayPolygon.geojson", driver='GeoJSON', encoding='utf-8')
```
- Extract and export the layer into CSV
```python 
with fiona.Env():
   layer = 'BoulderPoint'
   with fiona.open(fgdb_path, layer=layer) as src:
    gdf = gpd.GeoDataFrame.from_features(src)

   gdf['centroid_x'] = gdf.centroid.x
   gdf['centroid_y'] = gdf.centroid.y
   gdf.to_csv("BoulderPoint.csv", index=False, encoding='utf-8')
```

Remarks:
- The sample and demo code is for the Google Colab/ similar Jupyter notebook environment. 
- The download link of the data subject to the LandsD direct link, it may be changes over time, check from the HKMS2.0 or CSDI before complied.
