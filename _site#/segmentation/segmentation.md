# 1. Install planetary_computer and import other libraries


```python
# Install libraries
!pip install planetary_computer
!pip install odc-stac
!pip install osmnx
!pip install rioxarray
!pip install zarr
!pip install geojson

# Import libraries
from urllib.parse import urlparse
from pystac.extensions.eo import EOExtension as eo

import matplotlib.pyplot as plt
import planetary_computer
import pystac
import pystac_client
import odc.stac
import rich.table
import osmnx as ox
import folium
import numpy as np
import rioxarray
import rasterio
import xarray as xr
import zarr
import os
```

    Requirement already satisfied: planetary_computer in /usr/local/lib/python3.10/dist-packages (1.0.0)
    Requirement already satisfied: click>=7.1 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (8.1.7)
    Requirement already satisfied: pydantic>=1.7.3 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (2.6.4)
    Requirement already satisfied: pystac>=1.0.0 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (1.10.0)
    Requirement already satisfied: pystac-client>=0.2.0 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (0.7.6)
    Requirement already satisfied: pytz>=2020.5 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (2023.4)
    Requirement already satisfied: requests>=2.25.1 in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (2.31.0)
    Requirement already satisfied: packaging in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (24.0)
    Requirement already satisfied: python-dotenv in /usr/local/lib/python3.10/dist-packages (from planetary_computer) (1.0.1)
    Requirement already satisfied: annotated-types>=0.4.0 in /usr/local/lib/python3.10/dist-packages (from pydantic>=1.7.3->planetary_computer) (0.6.0)
    Requirement already satisfied: pydantic-core==2.16.3 in /usr/local/lib/python3.10/dist-packages (from pydantic>=1.7.3->planetary_computer) (2.16.3)
    Requirement already satisfied: typing-extensions>=4.6.1 in /usr/local/lib/python3.10/dist-packages (from pydantic>=1.7.3->planetary_computer) (4.11.0)
    Requirement already satisfied: python-dateutil>=2.7.0 in /usr/local/lib/python3.10/dist-packages (from pystac>=1.0.0->planetary_computer) (2.8.2)
    Requirement already satisfied: charset-normalizer<4,>=2 in /usr/local/lib/python3.10/dist-packages (from requests>=2.25.1->planetary_computer) (3.3.2)
    Requirement already satisfied: idna<4,>=2.5 in /usr/local/lib/python3.10/dist-packages (from requests>=2.25.1->planetary_computer) (3.6)
    Requirement already satisfied: urllib3<3,>=1.21.1 in /usr/local/lib/python3.10/dist-packages (from requests>=2.25.1->planetary_computer) (2.0.7)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.10/dist-packages (from requests>=2.25.1->planetary_computer) (2024.2.2)
    Requirement already satisfied: jsonschema~=4.18 in /usr/local/lib/python3.10/dist-packages (from pystac>=1.0.0->planetary_computer) (4.19.2)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.10/dist-packages (from python-dateutil>=2.7.0->pystac>=1.0.0->planetary_computer) (1.16.0)
    Requirement already satisfied: attrs>=22.2.0 in /usr/local/lib/python3.10/dist-packages (from jsonschema~=4.18->pystac>=1.0.0->planetary_computer) (23.2.0)
    Requirement already satisfied: jsonschema-specifications>=2023.03.6 in /usr/local/lib/python3.10/dist-packages (from jsonschema~=4.18->pystac>=1.0.0->planetary_computer) (2023.12.1)
    Requirement already satisfied: referencing>=0.28.4 in /usr/local/lib/python3.10/dist-packages (from jsonschema~=4.18->pystac>=1.0.0->planetary_computer) (0.34.0)
    Requirement already satisfied: rpds-py>=0.7.1 in /usr/local/lib/python3.10/dist-packages (from jsonschema~=4.18->pystac>=1.0.0->planetary_computer) (0.18.0)
    Requirement already satisfied: odc-stac in /usr/local/lib/python3.10/dist-packages (0.3.9)
    Requirement already satisfied: affine in /usr/local/lib/python3.10/dist-packages (from odc-stac) (2.4.0)
    Requirement already satisfied: odc-geo>=0.3.0 in /usr/local/lib/python3.10/dist-packages (from odc-stac) (0.4.3)
    Requirement already satisfied: rasterio!=1.3.0,!=1.3.1,>=1.0.0 in /usr/local/lib/python3.10/dist-packages (from odc-stac) (1.3.10)
    Requirement already satisfied: dask[array] in /usr/local/lib/python3.10/dist-packages (from odc-stac) (2023.8.1)
    Requirement already satisfied: numpy>=1.20.0 in /usr/local/lib/python3.10/dist-packages (from odc-stac) (1.25.2)
    Requirement already satisfied: pandas in /usr/local/lib/python3.10/dist-packages (from odc-stac) (2.0.3)
    Requirement already satisfied: pystac<2,>=1.0.0 in /usr/local/lib/python3.10/dist-packages (from odc-stac) (1.10.0)
    Requirement already satisfied: toolz in /usr/local/lib/python3.10/dist-packages (from odc-stac) (0.12.1)
    Requirement already satisfied: xarray>=0.19 in /usr/local/lib/python3.10/dist-packages (from odc-stac) (2023.7.0)
    Requirement already satisfied: cachetools in /usr/local/lib/python3.10/dist-packages (from odc-geo>=0.3.0->odc-stac) (5.3.3)
    Requirement already satisfied: pyproj>=3.0.0 in /usr/local/lib/python3.10/dist-packages (from odc-geo>=0.3.0->odc-stac) (3.6.1)
    Requirement already satisfied: shapely in /usr/local/lib/python3.10/dist-packages (from odc-geo>=0.3.0->odc-stac) (2.0.3)
    Requirement already satisfied: python-dateutil>=2.7.0 in /usr/local/lib/python3.10/dist-packages (from pystac<2,>=1.0.0->odc-stac) (2.8.2)
    Requirement already satisfied: attrs in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (23.2.0)
    Requirement already satisfied: certifi in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (2024.2.2)
    Requirement already satisfied: click>=4.0 in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (8.1.7)
    Requirement already satisfied: cligj>=0.5 in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (0.7.2)
    Requirement already satisfied: snuggs>=1.4.1 in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (1.4.7)
    Requirement already satisfied: click-plugins in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (1.1.1)
    Requirement already satisfied: setuptools in /usr/local/lib/python3.10/dist-packages (from rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (67.7.2)
    Requirement already satisfied: packaging>=21.3 in /usr/local/lib/python3.10/dist-packages (from xarray>=0.19->odc-stac) (24.0)
    Requirement already satisfied: pytz>=2020.1 in /usr/local/lib/python3.10/dist-packages (from pandas->odc-stac) (2023.4)
    Requirement already satisfied: tzdata>=2022.1 in /usr/local/lib/python3.10/dist-packages (from pandas->odc-stac) (2024.1)
    Requirement already satisfied: cloudpickle>=1.5.0 in /usr/local/lib/python3.10/dist-packages (from dask[array]->odc-stac) (2.2.1)
    Requirement already satisfied: fsspec>=2021.09.0 in /usr/local/lib/python3.10/dist-packages (from dask[array]->odc-stac) (2023.6.0)
    Requirement already satisfied: partd>=1.2.0 in /usr/local/lib/python3.10/dist-packages (from dask[array]->odc-stac) (1.4.1)
    Requirement already satisfied: pyyaml>=5.3.1 in /usr/local/lib/python3.10/dist-packages (from dask[array]->odc-stac) (6.0.1)
    Requirement already satisfied: importlib-metadata>=4.13.0 in /usr/local/lib/python3.10/dist-packages (from dask[array]->odc-stac) (7.1.0)
    Requirement already satisfied: zipp>=0.5 in /usr/local/lib/python3.10/dist-packages (from importlib-metadata>=4.13.0->dask[array]->odc-stac) (3.18.1)
    Requirement already satisfied: locket in /usr/local/lib/python3.10/dist-packages (from partd>=1.2.0->dask[array]->odc-stac) (1.0.0)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.10/dist-packages (from python-dateutil>=2.7.0->pystac<2,>=1.0.0->odc-stac) (1.16.0)
    Requirement already satisfied: pyparsing>=2.1.6 in /usr/local/lib/python3.10/dist-packages (from snuggs>=1.4.1->rasterio!=1.3.0,!=1.3.1,>=1.0.0->odc-stac) (3.1.2)
    Requirement already satisfied: osmnx in /usr/local/lib/python3.10/dist-packages (1.9.2)
    Requirement already satisfied: geopandas>=0.12 in /usr/local/lib/python3.10/dist-packages (from osmnx) (0.13.2)
    Requirement already satisfied: networkx>=2.5 in /usr/local/lib/python3.10/dist-packages (from osmnx) (3.3)
    Requirement already satisfied: numpy>=1.20 in /usr/local/lib/python3.10/dist-packages (from osmnx) (1.25.2)
    Requirement already satisfied: pandas>=1.1 in /usr/local/lib/python3.10/dist-packages (from osmnx) (2.0.3)
    Requirement already satisfied: requests>=2.27 in /usr/local/lib/python3.10/dist-packages (from osmnx) (2.31.0)
    Requirement already satisfied: shapely>=2.0 in /usr/local/lib/python3.10/dist-packages (from osmnx) (2.0.3)
    Requirement already satisfied: fiona>=1.8.19 in /usr/local/lib/python3.10/dist-packages (from geopandas>=0.12->osmnx) (1.9.6)
    Requirement already satisfied: packaging in /usr/local/lib/python3.10/dist-packages (from geopandas>=0.12->osmnx) (24.0)
    Requirement already satisfied: pyproj>=3.0.1 in /usr/local/lib/python3.10/dist-packages (from geopandas>=0.12->osmnx) (3.6.1)
    Requirement already satisfied: python-dateutil>=2.8.2 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.1->osmnx) (2.8.2)
    Requirement already satisfied: pytz>=2020.1 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.1->osmnx) (2023.4)
    Requirement already satisfied: tzdata>=2022.1 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.1->osmnx) (2024.1)
    Requirement already satisfied: charset-normalizer<4,>=2 in /usr/local/lib/python3.10/dist-packages (from requests>=2.27->osmnx) (3.3.2)
    Requirement already satisfied: idna<4,>=2.5 in /usr/local/lib/python3.10/dist-packages (from requests>=2.27->osmnx) (3.6)
    Requirement already satisfied: urllib3<3,>=1.21.1 in /usr/local/lib/python3.10/dist-packages (from requests>=2.27->osmnx) (2.0.7)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.10/dist-packages (from requests>=2.27->osmnx) (2024.2.2)
    Requirement already satisfied: attrs>=19.2.0 in /usr/local/lib/python3.10/dist-packages (from fiona>=1.8.19->geopandas>=0.12->osmnx) (23.2.0)
    Requirement already satisfied: click~=8.0 in /usr/local/lib/python3.10/dist-packages (from fiona>=1.8.19->geopandas>=0.12->osmnx) (8.1.7)
    Requirement already satisfied: click-plugins>=1.0 in /usr/local/lib/python3.10/dist-packages (from fiona>=1.8.19->geopandas>=0.12->osmnx) (1.1.1)
    Requirement already satisfied: cligj>=0.5 in /usr/local/lib/python3.10/dist-packages (from fiona>=1.8.19->geopandas>=0.12->osmnx) (0.7.2)
    Requirement already satisfied: six in /usr/local/lib/python3.10/dist-packages (from fiona>=1.8.19->geopandas>=0.12->osmnx) (1.16.0)
    Requirement already satisfied: rioxarray in /usr/local/lib/python3.10/dist-packages (0.15.3)
    Requirement already satisfied: packaging in /usr/local/lib/python3.10/dist-packages (from rioxarray) (24.0)
    Requirement already satisfied: rasterio>=1.3 in /usr/local/lib/python3.10/dist-packages (from rioxarray) (1.3.10)
    Requirement already satisfied: xarray>=2022.3.0 in /usr/local/lib/python3.10/dist-packages (from rioxarray) (2023.7.0)
    Requirement already satisfied: pyproj>=3.3 in /usr/local/lib/python3.10/dist-packages (from rioxarray) (3.6.1)
    Requirement already satisfied: numpy>=1.23 in /usr/local/lib/python3.10/dist-packages (from rioxarray) (1.25.2)
    Requirement already satisfied: certifi in /usr/local/lib/python3.10/dist-packages (from pyproj>=3.3->rioxarray) (2024.2.2)
    Requirement already satisfied: affine in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (2.4.0)
    Requirement already satisfied: attrs in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (23.2.0)
    Requirement already satisfied: click>=4.0 in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (8.1.7)
    Requirement already satisfied: cligj>=0.5 in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (0.7.2)
    Requirement already satisfied: snuggs>=1.4.1 in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (1.4.7)
    Requirement already satisfied: click-plugins in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (1.1.1)
    Requirement already satisfied: setuptools in /usr/local/lib/python3.10/dist-packages (from rasterio>=1.3->rioxarray) (67.7.2)
    Requirement already satisfied: pandas>=1.4 in /usr/local/lib/python3.10/dist-packages (from xarray>=2022.3.0->rioxarray) (2.0.3)
    Requirement already satisfied: python-dateutil>=2.8.2 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.4->xarray>=2022.3.0->rioxarray) (2.8.2)
    Requirement already satisfied: pytz>=2020.1 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.4->xarray>=2022.3.0->rioxarray) (2023.4)
    Requirement already satisfied: tzdata>=2022.1 in /usr/local/lib/python3.10/dist-packages (from pandas>=1.4->xarray>=2022.3.0->rioxarray) (2024.1)
    Requirement already satisfied: pyparsing>=2.1.6 in /usr/local/lib/python3.10/dist-packages (from snuggs>=1.4.1->rasterio>=1.3->rioxarray) (3.1.2)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.10/dist-packages (from python-dateutil>=2.8.2->pandas>=1.4->xarray>=2022.3.0->rioxarray) (1.16.0)
    Requirement already satisfied: zarr in /usr/local/lib/python3.10/dist-packages (2.17.2)
    Requirement already satisfied: asciitree in /usr/local/lib/python3.10/dist-packages (from zarr) (0.3.3)
    Requirement already satisfied: numpy>=1.23 in /usr/local/lib/python3.10/dist-packages (from zarr) (1.25.2)
    Requirement already satisfied: numcodecs>=0.10.0 in /usr/local/lib/python3.10/dist-packages (from zarr) (0.12.1)
    Requirement already satisfied: fasteners in /usr/local/lib/python3.10/dist-packages (from zarr) (0.19)
    Requirement already satisfied: geojson in /usr/local/lib/python3.10/dist-packages (3.1.0)


# 2. Use pystac to import a stac catalog from microsoft planetary computer


```python
catalog = pystac_client.Client.open(
    "https://planetarycomputer.microsoft.com/api/stac/v1",
    modifier=planetary_computer.sign_inplace,
)
```

# 3. Choose an area, time of interest and dataset from planetary computer


```python
bbox = [3.771744,43.610725,4.335823,43.814463] # 738785.0528,6274273.2065,779106.8653,6298491.2641
year = "2021"
months = {
    "January": "01",
    "February": "02",
    "March": "03",
    "April": "04",
    "May": "05",
    "June": "06",
    "July": "07",
    "August": "08",
    "September": "09",
    "October": "10",
    "november": "11",
    "December": "12",
}
items = dict()

# Fetch the collection of interest and print available items
for name, number in months.items():
    datetime = f"{year}-{number}"
    search = catalog.search(
        collections=["modis-11A1-061"],
        bbox=bbox,
        datetime=datetime,
    )
    items[name] = search.item_collection()[0]

print(items)
print(len(items))
```

    {'January': <Item id=MYD11A1.A2021031.h18v04.061.2021041154946>, 'February': <Item id=MYD11A1.A2021059.h18v04.061.2021062101022>, 'March': <Item id=MYD11A1.A2021090.h18v04.061.2021092202346>, 'April': <Item id=MYD11A1.A2021120.h18v04.061.2021122015755>, 'May': <Item id=MYD11A1.A2021151.h18v04.061.2021152213240>, 'June': <Item id=MYD11A1.A2021181.h18v04.061.2021183192128>, 'July': <Item id=MYD11A1.A2021212.h18v04.061.2021213191344>, 'August': <Item id=MYD11A1.A2021243.h18v04.061.2021244183922>, 'September': <Item id=MYD11A1.A2021273.h18v04.061.2021277221831>, 'October': <Item id=MYD11A1.A2021304.h18v04.061.2021306213324>, 'november': <Item id=MYD11A1.A2021334.h18v04.061.2021335171453>, 'December': <Item id=MYD11A1.A2021365.h18v04.061.2022004015033>}
    12


## 3.1. Chose the right asset

Each item has several available assets, including the original HDF file and a Cloud-optimized GeoTIFF of each subdataset. In our case, our interest is the ` LST_Day_1km` asset, that corresponds to the temperature mesured during daytime



```python
rich_table = rich.table.Table("Key", "Title")
for key, asset in items["March"].assets.items():
    rich_table.add_row(key, asset.title)
rich_table

```




<pre style="white-space:pre;overflow-x:auto;line-height:normal;font-family:Menlo,'DejaVu Sans Mono',consolas,'Courier New',monospace">┏━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃<span style="font-weight: bold"> Key              </span>┃<span style="font-weight: bold"> Title                                                               </span>┃
┡━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ hdf              │ Source data containing all bands                                    │
│ QC_Day           │ Quality control for daytime LST and emissivity                      │
│ Emis_31          │ Band 31 emissivity                                                  │
│ Emis_32          │ Band 32 emissivity                                                  │
│ QC_Night         │ Quality control for nighttime LST and emissivity                    │
│ metadata         │ Federal Geographic Data Committee (FGDC) Metadata                   │
│ LST_Day_1km      │ Daily daytime 1km grid Land-surface Temperature                     │
│ Clear_day_cov    │ Day clear-sky coverage                                              │
│ Day_view_angl    │ View zenith angle of daytime Landsurface Temperature                │
│ Day_view_time    │ (local solar) Time of daytime Land-surface Temperature observation  │
│ LST_Night_1km    │ Daily nighttime 1km grid Land-surface Temperature                   │
│ Clear_night_cov  │ Night clear-sky coverage                                            │
│ Night_view_angl  │ View zenith angle of nighttime Landsurface Temperature              │
│ Night_view_time  │ (local solar) Time of nighttime Landsurface Temperature observation │
│ tilejson         │ TileJSON with default rendering                                     │
│ rendered_preview │ Rendered preview                                                    │
└──────────────────┴─────────────────────────────────────────────────────────────────────┘
</pre>




## 3.2. The MODIS coordinate reference system is a sinusoidal grid, which means that views in a naïve XY raster look skewed. For visualization purposes, we reproject to a spherical Mercator projection for intuitive, north-up visualization.


```python
data = odc.stac.load(
    items.values(),
    crs="EPSG:2154",
    bands="LST_Day_1km",
    resolution=500,
    bbox=bbox,
)
# Get data data of July as a sample
raster = items["July"].assets["LST_Day_1km"].extra_fields["raster:bands"]

# Use raster scale to redefine temperature in celsius
data = (data["LST_Day_1km"] * raster[0]["scale"]) - 273.15
data
```




<div><svg style="position: absolute; width: 0; height: 0; overflow: hidden">
<defs>
<symbol id="icon-database" viewBox="0 0 32 32">
<path d="M16 0c-8.837 0-16 2.239-16 5v4c0 2.761 7.163 5 16 5s16-2.239 16-5v-4c0-2.761-7.163-5-16-5z"></path>
<path d="M16 17c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
<path d="M16 26c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
</symbol>
<symbol id="icon-file-text2" viewBox="0 0 32 32">
<path d="M28.681 7.159c-0.694-0.947-1.662-2.053-2.724-3.116s-2.169-2.030-3.116-2.724c-1.612-1.182-2.393-1.319-2.841-1.319h-15.5c-1.378 0-2.5 1.121-2.5 2.5v27c0 1.378 1.122 2.5 2.5 2.5h23c1.378 0 2.5-1.122 2.5-2.5v-19.5c0-0.448-0.137-1.23-1.319-2.841zM24.543 5.457c0.959 0.959 1.712 1.825 2.268 2.543h-4.811v-4.811c0.718 0.556 1.584 1.309 2.543 2.268zM28 29.5c0 0.271-0.229 0.5-0.5 0.5h-23c-0.271 0-0.5-0.229-0.5-0.5v-27c0-0.271 0.229-0.5 0.5-0.5 0 0 15.499-0 15.5 0v7c0 0.552 0.448 1 1 1h7v19.5z"></path>
<path d="M23 26h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 22h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 18h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
</symbol>
</defs>
</svg>
<style>/* CSS stylesheet for displaying xarray objects in jupyterlab.
 *
 */

:root {
  --xr-font-color0: var(--jp-content-font-color0, rgba(0, 0, 0, 1));
  --xr-font-color2: var(--jp-content-font-color2, rgba(0, 0, 0, 0.54));
  --xr-font-color3: var(--jp-content-font-color3, rgba(0, 0, 0, 0.38));
  --xr-border-color: var(--jp-border-color2, #e0e0e0);
  --xr-disabled-color: var(--jp-layout-color3, #bdbdbd);
  --xr-background-color: var(--jp-layout-color0, white);
  --xr-background-color-row-even: var(--jp-layout-color1, white);
  --xr-background-color-row-odd: var(--jp-layout-color2, #eeeeee);
}

html[theme=dark],
body[data-theme=dark],
body.vscode-dark {
  --xr-font-color0: rgba(255, 255, 255, 1);
  --xr-font-color2: rgba(255, 255, 255, 0.54);
  --xr-font-color3: rgba(255, 255, 255, 0.38);
  --xr-border-color: #1F1F1F;
  --xr-disabled-color: #515151;
  --xr-background-color: #111111;
  --xr-background-color-row-even: #111111;
  --xr-background-color-row-odd: #313131;
}

.xr-wrap {
  display: block !important;
  min-width: 300px;
  max-width: 700px;
}

.xr-text-repr-fallback {
  /* fallback to plain text repr when CSS is not injected (untrusted notebook) */
  display: none;
}

.xr-header {
  padding-top: 6px;
  padding-bottom: 6px;
  margin-bottom: 4px;
  border-bottom: solid 1px var(--xr-border-color);
}

.xr-header > div,
.xr-header > ul {
  display: inline;
  margin-top: 0;
  margin-bottom: 0;
}

.xr-obj-type,
.xr-array-name {
  margin-left: 2px;
  margin-right: 10px;
}

.xr-obj-type {
  color: var(--xr-font-color2);
}

.xr-sections {
  padding-left: 0 !important;
  display: grid;
  grid-template-columns: 150px auto auto 1fr 20px 20px;
}

.xr-section-item {
  display: contents;
}

.xr-section-item input {
  display: none;
}

.xr-section-item input + label {
  color: var(--xr-disabled-color);
}

.xr-section-item input:enabled + label {
  cursor: pointer;
  color: var(--xr-font-color2);
}

.xr-section-item input:enabled + label:hover {
  color: var(--xr-font-color0);
}

.xr-section-summary {
  grid-column: 1;
  color: var(--xr-font-color2);
  font-weight: 500;
}

.xr-section-summary > span {
  display: inline-block;
  padding-left: 0.5em;
}

.xr-section-summary-in:disabled + label {
  color: var(--xr-font-color2);
}

.xr-section-summary-in + label:before {
  display: inline-block;
  content: '►';
  font-size: 11px;
  width: 15px;
  text-align: center;
}

.xr-section-summary-in:disabled + label:before {
  color: var(--xr-disabled-color);
}

.xr-section-summary-in:checked + label:before {
  content: '▼';
}

.xr-section-summary-in:checked + label > span {
  display: none;
}

.xr-section-summary,
.xr-section-inline-details {
  padding-top: 4px;
  padding-bottom: 4px;
}

.xr-section-inline-details {
  grid-column: 2 / -1;
}

.xr-section-details {
  display: none;
  grid-column: 1 / -1;
  margin-bottom: 5px;
}

.xr-section-summary-in:checked ~ .xr-section-details {
  display: contents;
}

.xr-array-wrap {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 20px auto;
}

.xr-array-wrap > label {
  grid-column: 1;
  vertical-align: top;
}

.xr-preview {
  color: var(--xr-font-color3);
}

.xr-array-preview,
.xr-array-data {
  padding: 0 5px !important;
  grid-column: 2;
}

.xr-array-data,
.xr-array-in:checked ~ .xr-array-preview {
  display: none;
}

.xr-array-in:checked ~ .xr-array-data,
.xr-array-preview {
  display: inline-block;
}

.xr-dim-list {
  display: inline-block !important;
  list-style: none;
  padding: 0 !important;
  margin: 0;
}

.xr-dim-list li {
  display: inline-block;
  padding: 0;
  margin: 0;
}

.xr-dim-list:before {
  content: '(';
}

.xr-dim-list:after {
  content: ')';
}

.xr-dim-list li:not(:last-child):after {
  content: ',';
  padding-right: 5px;
}

.xr-has-index {
  font-weight: bold;
}

.xr-var-list,
.xr-var-item {
  display: contents;
}

.xr-var-item > div,
.xr-var-item label,
.xr-var-item > .xr-var-name span {
  background-color: var(--xr-background-color-row-even);
  margin-bottom: 0;
}

.xr-var-item > .xr-var-name:hover span {
  padding-right: 5px;
}

.xr-var-list > li:nth-child(odd) > div,
.xr-var-list > li:nth-child(odd) > label,
.xr-var-list > li:nth-child(odd) > .xr-var-name span {
  background-color: var(--xr-background-color-row-odd);
}

.xr-var-name {
  grid-column: 1;
}

.xr-var-dims {
  grid-column: 2;
}

.xr-var-dtype {
  grid-column: 3;
  text-align: right;
  color: var(--xr-font-color2);
}

.xr-var-preview {
  grid-column: 4;
}

.xr-index-preview {
  grid-column: 2 / 5;
  color: var(--xr-font-color2);
}

.xr-var-name,
.xr-var-dims,
.xr-var-dtype,
.xr-preview,
.xr-attrs dt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding-right: 10px;
}

.xr-var-name:hover,
.xr-var-dims:hover,
.xr-var-dtype:hover,
.xr-attrs dt:hover {
  overflow: visible;
  width: auto;
  z-index: 1;
}

.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  display: none;
  background-color: var(--xr-background-color) !important;
  padding-bottom: 5px !important;
}

.xr-var-attrs-in:checked ~ .xr-var-attrs,
.xr-var-data-in:checked ~ .xr-var-data,
.xr-index-data-in:checked ~ .xr-index-data {
  display: block;
}

.xr-var-data > table {
  float: right;
}

.xr-var-name span,
.xr-var-data,
.xr-index-name div,
.xr-index-data,
.xr-attrs {
  padding-left: 25px !important;
}

.xr-attrs,
.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  grid-column: 1 / -1;
}

dl.xr-attrs {
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 125px auto;
}

.xr-attrs dt,
.xr-attrs dd {
  padding: 0;
  margin: 0;
  float: left;
  padding-right: 10px;
  width: auto;
}

.xr-attrs dt {
  font-weight: normal;
  grid-column: 1;
}

.xr-attrs dt:hover span {
  display: inline-block;
  background: var(--xr-background-color);
  padding-right: 10px;
}

.xr-attrs dd {
  grid-column: 2;
  white-space: pre-wrap;
  word-break: break-all;
}

.xr-icon-database,
.xr-icon-file-text2,
.xr-no-icon {
  display: inline-block;
  vertical-align: middle;
  width: 1em;
  height: 1.5em !important;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
}
</style><pre class='xr-text-repr-fallback'>&lt;xarray.DataArray &#x27;LST_Day_1km&#x27; (time: 12, y: 48, x: 92)&gt;
array([[[-273.15, -273.15, -273.15, ...,   16.55,   16.71,   16.71],
        [-273.15, -273.15, -273.15, ...,   16.55,   16.71,   16.71],
        [-273.15, -273.15, -273.15, ...,   16.73,   16.69,   16.69],
        ...,
        [  15.51,   15.21,   15.21, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   13.47,   14.21,   14.21]],

       [[-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15,   20.53,   20.53],
        ...,
        [  16.67,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [-273.15,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [-273.15,   17.83,   17.83, ...,   15.63,   15.97,   15.97]],

       [[  23.83,   23.27,   23.27, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.13,   30.55,   30.55],
        ...,
...
        ...,
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15]],

       [[   8.65,    8.65,    8.65, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.55,    9.47,    9.47],
        ...,
        [  10.17,   10.23,   10.23, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.91,    7.67,    7.67]],

       [[  16.45,   16.07,   16.07, ..., -273.15, -273.15, -273.15],
        [  16.59,   16.17,   16.17, ..., -273.15, -273.15, -273.15],
        [  16.59,   16.17,   16.17, ..., -273.15, -273.15, -273.15],
        ...,
        [  16.65,   16.99,   16.99, ..., -273.15, -273.15, -273.15],
        [  16.63,   16.79,   16.79, ..., -273.15, -273.15, -273.15],
        [  16.63,   16.79,   16.79, ..., -273.15, -273.15, -273.15]]])
Coordinates:
  * y            (y) float64 6.303e+06 6.302e+06 ... 6.28e+06 6.279e+06
  * x            (x) float64 7.622e+05 7.628e+05 ... 8.072e+05 8.078e+05
    spatial_ref  int32 2154
  * time         (time) datetime64[ns] 2021-01-31 2021-02-28 ... 2021-12-31</pre><div class='xr-wrap' style='display:none'><div class='xr-header'><div class='xr-obj-type'>xarray.DataArray</div><div class='xr-array-name'>'LST_Day_1km'</div><ul class='xr-dim-list'><li><span class='xr-has-index'>time</span>: 12</li><li><span class='xr-has-index'>y</span>: 48</li><li><span class='xr-has-index'>x</span>: 92</li></ul></div><ul class='xr-sections'><li class='xr-section-item'><div class='xr-array-wrap'><input id='section-9f97c33f-dcd5-4bab-99f4-e0f320b8c60a' class='xr-array-in' type='checkbox' checked><label for='section-9f97c33f-dcd5-4bab-99f4-e0f320b8c60a' title='Show/hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-array-preview xr-preview'><span>-273.1 -273.1 -273.1 -273.1 -273.1 ... -273.1 -273.1 -273.1 -273.1</span></div><div class='xr-array-data'><pre>array([[[-273.15, -273.15, -273.15, ...,   16.55,   16.71,   16.71],
        [-273.15, -273.15, -273.15, ...,   16.55,   16.71,   16.71],
        [-273.15, -273.15, -273.15, ...,   16.73,   16.69,   16.69],
        ...,
        [  15.51,   15.21,   15.21, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   13.47,   14.21,   14.21]],

       [[-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15,   20.53,   20.53],
        ...,
        [  16.67,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [-273.15,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [-273.15,   17.83,   17.83, ...,   15.63,   15.97,   15.97]],

       [[  23.83,   23.27,   23.27, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.13,   30.55,   30.55],
        ...,
...
        ...,
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15]],

       [[   8.65,    8.65,    8.65, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.55,    9.47,    9.47],
        ...,
        [  10.17,   10.23,   10.23, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.91,    7.67,    7.67]],

       [[  16.45,   16.07,   16.07, ..., -273.15, -273.15, -273.15],
        [  16.59,   16.17,   16.17, ..., -273.15, -273.15, -273.15],
        [  16.59,   16.17,   16.17, ..., -273.15, -273.15, -273.15],
        ...,
        [  16.65,   16.99,   16.99, ..., -273.15, -273.15, -273.15],
        [  16.63,   16.79,   16.79, ..., -273.15, -273.15, -273.15],
        [  16.63,   16.79,   16.79, ..., -273.15, -273.15, -273.15]]])</pre></div></div></li><li class='xr-section-item'><input id='section-84bc693b-d393-4e58-9245-4a820cba6937' class='xr-section-summary-in' type='checkbox'  checked><label for='section-84bc693b-d393-4e58-9245-4a820cba6937' class='xr-section-summary' >Coordinates: <span>(4)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>y</span></div><div class='xr-var-dims'>(y)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>6.303e+06 6.302e+06 ... 6.279e+06</div><input id='attrs-9252c29b-fce8-4ebf-8ceb-ee65d07765cb' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-9252c29b-fce8-4ebf-8ceb-ee65d07765cb' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-a89546bb-515f-488b-995a-b089b897e636' class='xr-var-data-in' type='checkbox'><label for='data-a89546bb-515f-488b-995a-b089b897e636' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>units :</span></dt><dd>metre</dd><dt><span>resolution :</span></dt><dd>-500.0</dd><dt><span>crs :</span></dt><dd>EPSG:2154</dd></dl></div><div class='xr-var-data'><pre>array([6302750., 6302250., 6301750., 6301250., 6300750., 6300250., 6299750.,
       6299250., 6298750., 6298250., 6297750., 6297250., 6296750., 6296250.,
       6295750., 6295250., 6294750., 6294250., 6293750., 6293250., 6292750.,
       6292250., 6291750., 6291250., 6290750., 6290250., 6289750., 6289250.,
       6288750., 6288250., 6287750., 6287250., 6286750., 6286250., 6285750.,
       6285250., 6284750., 6284250., 6283750., 6283250., 6282750., 6282250.,
       6281750., 6281250., 6280750., 6280250., 6279750., 6279250.])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>x</span></div><div class='xr-var-dims'>(x)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>7.622e+05 7.628e+05 ... 8.078e+05</div><input id='attrs-69d2752e-7594-432f-bac8-08489c7434f4' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-69d2752e-7594-432f-bac8-08489c7434f4' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-2d967486-598f-4d82-ae4f-28a464e2b625' class='xr-var-data-in' type='checkbox'><label for='data-2d967486-598f-4d82-ae4f-28a464e2b625' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>units :</span></dt><dd>metre</dd><dt><span>resolution :</span></dt><dd>500.0</dd><dt><span>crs :</span></dt><dd>EPSG:2154</dd></dl></div><div class='xr-var-data'><pre>array([762250., 762750., 763250., 763750., 764250., 764750., 765250., 765750.,
       766250., 766750., 767250., 767750., 768250., 768750., 769250., 769750.,
       770250., 770750., 771250., 771750., 772250., 772750., 773250., 773750.,
       774250., 774750., 775250., 775750., 776250., 776750., 777250., 777750.,
       778250., 778750., 779250., 779750., 780250., 780750., 781250., 781750.,
       782250., 782750., 783250., 783750., 784250., 784750., 785250., 785750.,
       786250., 786750., 787250., 787750., 788250., 788750., 789250., 789750.,
       790250., 790750., 791250., 791750., 792250., 792750., 793250., 793750.,
       794250., 794750., 795250., 795750., 796250., 796750., 797250., 797750.,
       798250., 798750., 799250., 799750., 800250., 800750., 801250., 801750.,
       802250., 802750., 803250., 803750., 804250., 804750., 805250., 805750.,
       806250., 806750., 807250., 807750.])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span>spatial_ref</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int32</div><div class='xr-var-preview xr-preview'>2154</div><input id='attrs-daf30bda-cd06-483b-83a4-5d5d85589caa' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-daf30bda-cd06-483b-83a4-5d5d85589caa' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-99989a6a-df47-456b-ae58-069fb7808cce' class='xr-var-data-in' type='checkbox'><label for='data-99989a6a-df47-456b-ae58-069fb7808cce' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>spatial_ref :</span></dt><dd>PROJCRS[&quot;RGF93 v1 / Lambert-93&quot;,BASEGEOGCRS[&quot;RGF93 v1&quot;,DATUM[&quot;Reseau Geodesique Francais 1993 v1&quot;,ELLIPSOID[&quot;GRS 1980&quot;,6378137,298.257222101,LENGTHUNIT[&quot;metre&quot;,1]]],PRIMEM[&quot;Greenwich&quot;,0,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433]],ID[&quot;EPSG&quot;,4171]],CONVERSION[&quot;Lambert-93&quot;,METHOD[&quot;Lambert Conic Conformal (2SP)&quot;,ID[&quot;EPSG&quot;,9802]],PARAMETER[&quot;Latitude of false origin&quot;,46.5,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8821]],PARAMETER[&quot;Longitude of false origin&quot;,3,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8822]],PARAMETER[&quot;Latitude of 1st standard parallel&quot;,49,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8823]],PARAMETER[&quot;Latitude of 2nd standard parallel&quot;,44,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8824]],PARAMETER[&quot;Easting at false origin&quot;,700000,LENGTHUNIT[&quot;metre&quot;,1],ID[&quot;EPSG&quot;,8826]],PARAMETER[&quot;Northing at false origin&quot;,6600000,LENGTHUNIT[&quot;metre&quot;,1],ID[&quot;EPSG&quot;,8827]]],CS[Cartesian,2],AXIS[&quot;easting (X)&quot;,east,ORDER[1],LENGTHUNIT[&quot;metre&quot;,1]],AXIS[&quot;northing (Y)&quot;,north,ORDER[2],LENGTHUNIT[&quot;metre&quot;,1]],USAGE[SCOPE[&quot;Engineering survey, topographic mapping.&quot;],AREA[&quot;France - onshore and offshore, mainland and Corsica (France métropolitaine including Corsica).&quot;],BBOX[41.15,-9.86,51.56,10.38]],ID[&quot;EPSG&quot;,2154]]</dd><dt><span>crs_wkt :</span></dt><dd>PROJCRS[&quot;RGF93 v1 / Lambert-93&quot;,BASEGEOGCRS[&quot;RGF93 v1&quot;,DATUM[&quot;Reseau Geodesique Francais 1993 v1&quot;,ELLIPSOID[&quot;GRS 1980&quot;,6378137,298.257222101,LENGTHUNIT[&quot;metre&quot;,1]]],PRIMEM[&quot;Greenwich&quot;,0,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433]],ID[&quot;EPSG&quot;,4171]],CONVERSION[&quot;Lambert-93&quot;,METHOD[&quot;Lambert Conic Conformal (2SP)&quot;,ID[&quot;EPSG&quot;,9802]],PARAMETER[&quot;Latitude of false origin&quot;,46.5,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8821]],PARAMETER[&quot;Longitude of false origin&quot;,3,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8822]],PARAMETER[&quot;Latitude of 1st standard parallel&quot;,49,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8823]],PARAMETER[&quot;Latitude of 2nd standard parallel&quot;,44,ANGLEUNIT[&quot;degree&quot;,0.0174532925199433],ID[&quot;EPSG&quot;,8824]],PARAMETER[&quot;Easting at false origin&quot;,700000,LENGTHUNIT[&quot;metre&quot;,1],ID[&quot;EPSG&quot;,8826]],PARAMETER[&quot;Northing at false origin&quot;,6600000,LENGTHUNIT[&quot;metre&quot;,1],ID[&quot;EPSG&quot;,8827]]],CS[Cartesian,2],AXIS[&quot;easting (X)&quot;,east,ORDER[1],LENGTHUNIT[&quot;metre&quot;,1]],AXIS[&quot;northing (Y)&quot;,north,ORDER[2],LENGTHUNIT[&quot;metre&quot;,1]],USAGE[SCOPE[&quot;Engineering survey, topographic mapping.&quot;],AREA[&quot;France - onshore and offshore, mainland and Corsica (France métropolitaine including Corsica).&quot;],BBOX[41.15,-9.86,51.56,10.38]],ID[&quot;EPSG&quot;,2154]]</dd><dt><span>semi_major_axis :</span></dt><dd>6378137.0</dd><dt><span>semi_minor_axis :</span></dt><dd>6356752.314140356</dd><dt><span>inverse_flattening :</span></dt><dd>298.257222101</dd><dt><span>reference_ellipsoid_name :</span></dt><dd>GRS 1980</dd><dt><span>longitude_of_prime_meridian :</span></dt><dd>0.0</dd><dt><span>prime_meridian_name :</span></dt><dd>Greenwich</dd><dt><span>geographic_crs_name :</span></dt><dd>RGF93 v1</dd><dt><span>horizontal_datum_name :</span></dt><dd>Reseau Geodesique Francais 1993 v1</dd><dt><span>projected_crs_name :</span></dt><dd>RGF93 v1 / Lambert-93</dd><dt><span>grid_mapping_name :</span></dt><dd>lambert_conformal_conic</dd><dt><span>standard_parallel :</span></dt><dd>(49.0, 44.0)</dd><dt><span>latitude_of_projection_origin :</span></dt><dd>46.5</dd><dt><span>longitude_of_central_meridian :</span></dt><dd>3.0</dd><dt><span>false_easting :</span></dt><dd>700000.0</dd><dt><span>false_northing :</span></dt><dd>6600000.0</dd><dt><span>GeoTransform :</span></dt><dd>762000.0 500.0 0.0 6303000.0 0.0 -500.0</dd></dl></div><div class='xr-var-data'><pre>array(2154, dtype=int32)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>time</span></div><div class='xr-var-dims'>(time)</div><div class='xr-var-dtype'>datetime64[ns]</div><div class='xr-var-preview xr-preview'>2021-01-31 ... 2021-12-31</div><input id='attrs-0feba3e1-ef34-4519-ac31-4ada9657f1c6' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-0feba3e1-ef34-4519-ac31-4ada9657f1c6' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-9bf52848-d0ba-481a-abd2-71ec2e14c479' class='xr-var-data-in' type='checkbox'><label for='data-9bf52848-d0ba-481a-abd2-71ec2e14c479' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([&#x27;2021-01-31T00:00:00.000000000&#x27;, &#x27;2021-02-28T00:00:00.000000000&#x27;,
       &#x27;2021-03-31T00:00:00.000000000&#x27;, &#x27;2021-04-30T00:00:00.000000000&#x27;,
       &#x27;2021-05-31T00:00:00.000000000&#x27;, &#x27;2021-06-30T00:00:00.000000000&#x27;,
       &#x27;2021-07-31T00:00:00.000000000&#x27;, &#x27;2021-08-31T00:00:00.000000000&#x27;,
       &#x27;2021-09-30T00:00:00.000000000&#x27;, &#x27;2021-10-31T00:00:00.000000000&#x27;,
       &#x27;2021-11-30T00:00:00.000000000&#x27;, &#x27;2021-12-31T00:00:00.000000000&#x27;],
      dtype=&#x27;datetime64[ns]&#x27;)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-30fb19a0-78e9-4f5f-8661-5667a3444c02' class='xr-section-summary-in' type='checkbox'  ><label for='section-30fb19a0-78e9-4f5f-8661-5667a3444c02' class='xr-section-summary' >Indexes: <span>(3)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-index-name'><div>y</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-ddc7679f-68ed-4640-9856-89e5643e5a68' class='xr-index-data-in' type='checkbox'/><label for='index-ddc7679f-68ed-4640-9856-89e5643e5a68' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([6302750.0, 6302250.0, 6301750.0, 6301250.0, 6300750.0, 6300250.0,
       6299750.0, 6299250.0, 6298750.0, 6298250.0, 6297750.0, 6297250.0,
       6296750.0, 6296250.0, 6295750.0, 6295250.0, 6294750.0, 6294250.0,
       6293750.0, 6293250.0, 6292750.0, 6292250.0, 6291750.0, 6291250.0,
       6290750.0, 6290250.0, 6289750.0, 6289250.0, 6288750.0, 6288250.0,
       6287750.0, 6287250.0, 6286750.0, 6286250.0, 6285750.0, 6285250.0,
       6284750.0, 6284250.0, 6283750.0, 6283250.0, 6282750.0, 6282250.0,
       6281750.0, 6281250.0, 6280750.0, 6280250.0, 6279750.0, 6279250.0],
      dtype=&#x27;float64&#x27;, name=&#x27;y&#x27;))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>x</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-9a452836-65f5-4b52-be08-79da5978d97f' class='xr-index-data-in' type='checkbox'/><label for='index-9a452836-65f5-4b52-be08-79da5978d97f' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([762250.0, 762750.0, 763250.0, 763750.0, 764250.0, 764750.0, 765250.0,
       765750.0, 766250.0, 766750.0, 767250.0, 767750.0, 768250.0, 768750.0,
       769250.0, 769750.0, 770250.0, 770750.0, 771250.0, 771750.0, 772250.0,
       772750.0, 773250.0, 773750.0, 774250.0, 774750.0, 775250.0, 775750.0,
       776250.0, 776750.0, 777250.0, 777750.0, 778250.0, 778750.0, 779250.0,
       779750.0, 780250.0, 780750.0, 781250.0, 781750.0, 782250.0, 782750.0,
       783250.0, 783750.0, 784250.0, 784750.0, 785250.0, 785750.0, 786250.0,
       786750.0, 787250.0, 787750.0, 788250.0, 788750.0, 789250.0, 789750.0,
       790250.0, 790750.0, 791250.0, 791750.0, 792250.0, 792750.0, 793250.0,
       793750.0, 794250.0, 794750.0, 795250.0, 795750.0, 796250.0, 796750.0,
       797250.0, 797750.0, 798250.0, 798750.0, 799250.0, 799750.0, 800250.0,
       800750.0, 801250.0, 801750.0, 802250.0, 802750.0, 803250.0, 803750.0,
       804250.0, 804750.0, 805250.0, 805750.0, 806250.0, 806750.0, 807250.0,
       807750.0],
      dtype=&#x27;float64&#x27;, name=&#x27;x&#x27;))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>time</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-d098c0ec-5943-40ee-b5bc-ebef011bdf19' class='xr-index-data-in' type='checkbox'/><label for='index-d098c0ec-5943-40ee-b5bc-ebef011bdf19' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(DatetimeIndex([&#x27;2021-01-31&#x27;, &#x27;2021-02-28&#x27;, &#x27;2021-03-31&#x27;, &#x27;2021-04-30&#x27;,
               &#x27;2021-05-31&#x27;, &#x27;2021-06-30&#x27;, &#x27;2021-07-31&#x27;, &#x27;2021-08-31&#x27;,
               &#x27;2021-09-30&#x27;, &#x27;2021-10-31&#x27;, &#x27;2021-11-30&#x27;, &#x27;2021-12-31&#x27;],
              dtype=&#x27;datetime64[ns]&#x27;, name=&#x27;time&#x27;, freq=None))</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-5e55ce86-d78d-4531-831e-c2b26aee177d' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-5e55ce86-d78d-4531-831e-c2b26aee177d' class='xr-section-summary'  title='Expand/collapse section'>Attributes: <span>(0)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><dl class='xr-attrs'></dl></div></li></ul></div></div>



# 4. Interpolate data and fill missing values

## 4.1. There are many nodata values. Lets interpolate missing values using rioxarray. But me must first define our nodata value. In this case, we can define as -273.15, since Modis defines nodata value as 0


```python
data_with_nodata = data.rio.write_nodata(-273.15, inplace=True)
print(f'No data value is {data_with_nodata.rio.nodata}')
```

    No data value is -273.15


## 4.2. We plot the month of mars


```python
data_with_nodata[2].plot()
```




    <matplotlib.collections.QuadMesh at 0x798254418730>




    
![png](output_15_1.png)
    


## 4.3. With the nodata defined, we can interpolate na's using the nearest neighbour method and fill missing data


```python
# Interpolate missing values
data_interpolated=data_with_nodata.rio.interpolate_na(method='nearest')

# Plot march again
data_interpolated[2].plot()
```




    <matplotlib.collections.QuadMesh at 0x798254a2e380>




    
![png](output_17_1.png)
    


## 4.4. Plot all the months together


```python
g = data_interpolated.plot.imshow(cmap="Spectral_r", col="time", vmin=-10, vmax=60, size=4,col_wrap=4)
datetimes = data.time.to_pandas().dt.strftime("%B")

for ax, datetime in zip(g.axes.flat, datetimes):
    ax.set_title(datetime)
```

    <ipython-input-39-d1854156f2f9>:4: DeprecationWarning: self.axes is deprecated since 2022.11 in order to align with matplotlibs plt.subplots, use self.axs instead.
      for ax, datetime in zip(g.axes.flat, datetimes):



    
![png](output_19_1.png)
    


# 5. Plot images with folium

## 5.1. Create bbox with Fiona


```python
import fiona
import rasterio
import rasterio.mask
import json
import geojson
from fiona.crs import from_epsg


from shapely.geometry import mapping
import json

import requests
response = requests.get('http://app3.ouicodedata.com/collections/agropolis-zone/items')

with open('./response.json', 'w') as outfile:
  json.dump(response.json(), outfile)

# Load JSON data from a file
with open('./response.json','r') as infile:
    with open('./agropoilis_zone.geojson', 'w') as outfile:
      data = json.load(infile)
      geojson.dump(data,outfile)

with fiona.open("./agropoilis_zone.geojson", "r",crs=from_epsg(2154)) as geojson:
    shapes = [feature["geometry"] for feature in geojson]
    print(from_epsg(2154))


```

    EPSG:2154



```python
import os
from rasterio import windows
import random
from rasterio import features
from rasterio import warp
from rasterio.windows import get_data_window
import rasterio.mask

for i,d in enumerate(data_interpolated,start=1):
    if not os.path.exists('temp'): os.makedirs('temp')
    temp_file_name = '_'.join(['./temp/modis_tile',list(months.values())[i-1],'.tiff'])
    d.rio.to_raster(temp_file_name)
    with rasterio.open(temp_file_name) as src:
        out_image, out_transform = rasterio.mask.mask(src, shapes, crop=True)
        out_meta = src.meta
        out_meta.update({"driver": "GTiff",
                 "height": out_image.shape[1],
                 "width": out_image.shape[2],
                 "transform": out_transform})
        if not os.path.exists('cropped'): os.makedirs('cropped')
        croped_file_name = '_'.join(['./cropped/cropped_modis_tile',list(months.values())[i-1],'.tiff'])
        with rasterio.open(croped_file_name, "w", **out_meta) as dest:
            dest.write(out_image)

```

## 5.3. Export images and plot then together with Folium


```python
from pyproj import Transformer
f = folium.Figure(width=700, height=500)
m = folium.Map(location=(43.7237837,4.0003508)).add_to(f)
from rasterio.windows import Window


for i,d in enumerate(data,start=1):
    temp_file_name = '_'.join(['./temp/modis_tile',list(months.values())[i-1],'.tiff'])
    dst_crs = 'EPSG:4326'
    with rasterio.open(temp_file_name) as src:

        img = src.read()

        src_crs = src.crs['init'].upper()
        min_lon, min_lat, max_lon, max_lat = src.bounds

    ## Conversion from UTM to WGS84 CRS
    bounds_orig = [[min_lat, min_lon], [max_lat, max_lon]]

    bounds_fin = []

    for item in bounds_orig:
        #converting to lat/lon
        lat = item[0]
        lon = item[1]

        proj = Transformer.from_crs(int(src_crs.split(":")[1]), int(dst_crs.split(":")[1]), always_xy=True)

        lon_n, lat_n = proj.transform(lon, lat)

        bounds_fin.append([lat_n, lon_n])

    # Finding the centre latitude & longitude
    centre_lon = bounds_fin[0][1] + (bounds_fin[1][1] - bounds_fin[0][1])/2
    centre_lat = bounds_fin[0][0] + (bounds_fin[1][0] - bounds_fin[0][0])/2


    layer = folium.raster_layers.ImageOverlay(
        image = img.transpose(1, 2, 0),
        name=list(months.keys())[i-1],    # add a name to the layer
        bounds = bounds_fin,
        opacity=0.7,
        interactive=False,
        cross_origin=False,
        zindex=1,
    )
    # Overlay the image
    m.add_child(layer)

folium.LayerControl().add_to(m)
m
```




<iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-1.12.4.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_c411caddeadbebf70f7cebfd52330cd5 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;


                &lt;style&gt;
                    .leaflet-image-layer {
                        /* old android/safari*/
                        image-rendering: -webkit-optimize-contrast;
                        image-rendering: crisp-edges; /* safari */
                        image-rendering: pixelated; /* chrome */
                        image-rendering: -moz-crisp-edges; /* firefox */
                        image-rendering: -o-crisp-edges; /* opera */
                        -ms-interpolation-mode: nearest-neighbor; /* ie */
                    }
                &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_c411caddeadbebf70f7cebfd52330cd5&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_c411caddeadbebf70f7cebfd52330cd5 = L.map(
                &quot;map_c411caddeadbebf70f7cebfd52330cd5&quot;,
                {
                    center: [43.7237837, 4.0003508],
                    crs: L.CRS.EPSG3857,
                    zoom: 10,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_8ebb7bdf73674872bc467e44a609f99a = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca target=\&quot;_blank\&quot; href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca target=\&quot;_blank\&quot; href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_06ec3d8b1ee2472e0dee5d2551cc2776 = L.imageOverlay(
                &quot;data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAFwAAAAwCAYAAACc2R15AAAJmUlEQVR42uXbBY8USxSG4f7DBIIEJ0hwd0Jwd3d3d3d3CAGChzA330neztnaap3ZAXJv8iULd5nuerq66lR1TzJ58uRGUYYNG9aYMWOGZd26dZYtW7bkZsmSJZkZM2ZMmgkTJjSdlStX5ubIkSOW2P/bu3dvh6xfv96ydu3aNCNGjCidBQsWWG7cuGH5/Pmz5du3b5YkD3r8+PGGXQW8LHQrwfPQwW4GvCx6z549LW0BrwrdDvSq2HXB+/btawH8x48faYDOBR84cGCawYMHWyZNmmTZvHmz5dy5c5bz589b6kB3FfiaNWssYG/durUT9vLlyyuB56FXAp87d24mdjPgZbFbCQ7awYMHLceOHbOE4MKuCs4dHgPv3r17h3z48CHN+/fvO8TASYgdguvEycmTJy379++3LF682FIF+k+Ag10X3KOH0JXBY+iAe+ws8DrYrQLnPHwuX75sOXHiRFPgHlsZN26cZeTIkYXgjx8/tjx9+tTSCTxEj2Eru3btsujkxo4da/lT4DFswIVdFTyvd4fg5PDhw5YLFy5Yrl27lmIXgvfp0yfNokWLugScO4e0Evzo0aMWzS2UZ/v27bNs2rQpzenTp9Mw/OiiTJs2rUPqgvtcvXrVUgpcCSsZykPh8ztloHVyIXZZ8BUrVkQTw24nONiVwbt169YB24OH1UwIzkSlxJBJDPvWrVuWssB5AXvHjh2W69evW4BljFZOnTqVhqFn+/btjSlTpnSISkwPjkEMnOoNYP5MqZpQwgm7CDxEP3DggMVjE4+ch+3BlTrIPkAXgW/YsKETuLA9OEgkCxwHhc/juJTNVHG54IMGDWosXLjQwkGXLl1qUeOYaHbv3p1m4sSJtbG7Ap3Fz5UrVyycs8AVkHXuHjzE9ujCloEA6ZClwXXQ+fPndwIXdlVwYcfAq2A3C65xXMDNgCvTp09P44c52h+Cg86ccenSJQvH37lzpyVhLKZsoqgfMmSIZdasWRYmHW7JZcuWpatKTj4GXhW7GXQmTjWQnw8dOmRhTKXH8ffbtm2z8BnNgCtdAi7sMuB1sZsBp4Ex8Nu3b6cQtGfPnj2dPmf06NGZARvgefPmWfjzxo0bLVw47jTdVVooJshT5Gt4UdiIETYzraJbj4vEvgqfsXr1asvQoUMtIfSZM2csrQanpBMgDaVaYUwVdhlwAP8YuCLkZsGrYJcFB5qoUYAI2/+/sN5mElNi4Fnw+uzZs2d3ypw5cyzhEOMvrFa+SbilyRjN3zMhsndCT1KAphRk9clnhdg3b94sDV6EHmKH4B63CFwJoZVRo0ZZevXqlSaG3WXg/tYtAw402K0A9w8VfFh4sIABWA1VuNWZo3iwImw9QCkLnoXOqhR4TFQa4qbJOhk+fHhDYXLkxBguwNVwwskDytMeej+1KOOnoM+ePWupAu7LsyLoZsH9U6s64OE2wF8Dfu/ePUu4x+DjocnUqVMLsf2kzvEAp+FULYzFanP4mBBoPVpU+vfvbwnBPXoWuN9bYRGkDpeCs1xlWUt9KuDjx49baCC9jovDJMoFoZfpQj18+NCSBx6D9uCxxLDbCc7YHwMPN7OErba/efOmOXB6eliX6yJQjt2/f99y9+5di9+/yINWVMYVwSt+D4eGAs85c1tr1czEyq4hxwCNYYdqSwmhfTw4x6cMVehsBg40twI9AHC/rPcrOaUZ8CJsLcM54SroXQVOG2PgoMewX758mebTp0+dwf1+MQdj1ckQQjXCrchKE3C/18xkyVUugs4Dz0NnHQEwS3g6Dm3yFRlVFu1gXO7du7eF9utuLgJXwueXpAM4m1OrVq2ycGLa5/5T4Gw0xcCz4FsFzsJP4L4c5bFcFrbG6SxwXgb6/v17IwEv3NsWOLfYgAEDLOwHZy1jNd7TYMDBo+Tjdg43+YnmihD8wYMHheDcobSDyomhRaDgcA7h2gNsUhU8C/2vBWdyjoEXoeeBq1LQ4g08j9oKcLCz0D9+/GjRi0EJdTa7akwuQmNMY2+8X79+FhYErEa5JQXOv7948aKF7UpOht8NeyjYVcA9urD9gxDWAjxjzAJXmgHX3lEIrnOtBE4vbRe4x64KTsLanjmDrQU+uy44G3NZCcGVt2/fWhhSfv782UiAplxTb2ARwcFYCLB7yKYUCwUdkMkJAODDZTsAulBMZF0NHn5+CAt6FngR9j8LzjlwfO4OVqtKWXCqFOaSOuBloGPod+7csbx7987Ci52/f/9uJOGSWOBs0jOh+jEL8HCjh9KQMHnxWTzioteyV6PwnLFV4GB7cI8e29ple5dUxa4FzgSTB64NLsbwLGwPzvBAWHFqxcrvMtGB9OzZMwu3ogfPQvdvx9IO3xa/pVCEXYTuj8U+ktrKu4zPnz+3fP361fLr1y+L/kvCyUUJeyWNYkcRcN5AUqqAs0VQF9y/H8hXPML3vz12EXgMOws8PA7YMXB6NuDWw6uCa2zz0GE8eIidBc5zP+YTwLklHz16lP57j50H7o/psauAh+isyn10LHZJmad4czYKzi9p7GN4YcxjvKWmZTIpA+4bnFdd+Jd2qoL36NEjBfdvHlQBz8MGfObMmZlR/Q84x6NKef36teXLly8WXQQDZ3Ly71Mr1NKcaNZLjTH0ImjCatWvWHnFl00fnTznwCt3wg7BPbpWmexUcqdSGJTFVtQBWwquHlwXnJPy2DSGiqBZcE6+CjjL+rrg/oGG2p8HrgAebo+EbdXdm2gSoowB3D+hV7gAeScajouA63M4IKtMj+xLOCoLJscnT56kWNTsrAUA540o1gHajw7B9TkKHSv89kYMGux/Epy0E1zQrPTY06BA4Dg8MMjDVlRI5IGzNcDGHNvUTKyU1LoDE30NgtqYA3CFmABZceo1tqzbsAw4e+7+bVQPztcyGLv1M/s6nIt/vzsEZ8igZ9cFD7FJHvY/A67qRz0YbMD52YOrh8fAw9KThx7Av3jxwsKdw9Ci3w1Xp1nYMXB/Lgovf/IVHB7gqLJjMk9UoDNJsTQFnAmJ/Wo1jvc5FLCF6PfCFUpJ/RxeQB/AFRYMwmGGD5/YhOjtBAc9hG4LOH9Hr2XiqALO7wBO/f3q1atccI8eW6azJcHnstYAmnWG368hDDcxbJIHzqsVCtva/k22RA2kB1CC8ayPW0ffkOCbyDzRzgLn31IB+K9jCAFsLSjKgIMegitZm1D/O3BVH/qZlSP4QLBUplyiIToXxm6tLn2qgLMSZY+GGp1JTQ0HPfzWGRWZf5knTBa4x1Z0DsxfDHf/AT3t4WDmuiQpAAAAAElFTkSuQmCC&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_a89a77ffe85cc350b6d5e694476e9cb5 = L.imageOverlay(
                &quot;data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAFwAAAAwCAYAAACc2R15AAAE6ElEQVR42u3b10vsQBTH8f3/3/VFsT0oCnaxd8XesPeuiBUbKiJ7OQd+4Tg7k53JJpvZvQpf8Hr3pnySTGJukqmpqcmm1fr6uraFhQUvm5+f52ZnZ7nh4WFuaGiIm5iY4La3t7N7e3u/+v7+5jI+gvuKHhUc2KmCh2H7Ck/YLS0tQc3NzVx3dzeHDVDS4D6hS+wwcEqHXTLgtMf4AF5dXa2to6MjJ6CXLPjU1JSX2CbwgYGBHOzUwF2wAb6zs8PoacCHYevAOzs7GZyik2vJgxcTPh82RcBqEpxaWlriSho8afgo0N6Bn5+fxw6eBHwh2GUDboMeB3yh2FRfXx+HZSpr8ELg48D2BpywiwmuIhQL2ztwV3QJXii6aSPEie0FuMT2CVwWF/YfuGOFYqcOvru7mxp4FPSenh6ObrmWDbgLugqe9F4uwefm5rg4wGtra5MHJ+y0wV3RdeBbW1scLYvNNAhXV6YY2OUGLjs7O/uVCTpxcImdBHiS6IWA50NPFdwW/Q/cEft/Aw9DzxQD2xdwW/SSATdhJwWeFHoc4Cb0TDGww8Bt0P/AHbF9A8+HHhe4Dj0yeHt7e1Ca4Ens5V6AS2BX7Hzg+dCTAA9Djwv88PCQ04JjJv39/b8yQVOnp6f/Nfj4+Hh2cnIyFDsHvLe3N0tJ8DBkie0L+MHBQazoXV1dHFkAfHl5mcN/ChM2wKmTkxOOTCQ2dXFxERSA4wlQusOVD/vm5iZ28DB0G/Co6CZwHOEu4DApa3Bgx42ugs/MzHDT09OcCk7zBvDR0dGvtOCUzVDy8PDgNTieQ4zrikVixwpuix0FnJ4itQE3obuCu6KHgUtsEzhOqLqd6/r6mgvAV1dXs9Tg4GA2bG8Htiv48fFx8Kx0uYED2gQObCtwiS6xXcAJW4JHRTeBq9gSfG1trSB0dV1U8I2NjZwwbwmNsG4B+OjoaM41OD4UBRzYKrgNug04xscwcGQLbjM8qr/4kIXa5eUlh2tyuW5GcPkhV3CJTdGh5IKOZUISDwGcPp8PHGGZdeC2J38VXIduAudffOiaUj0kdAi24Co2wF3QbbEBLtFN2Pv7+8Eyy1ywTeBYhqurK059NALvAEUGv7u7C7YiVlwHrYKr6EBSf1GQex7GzsXFxSAdOABpui7gLpe2RQP//PzkooB/fHz8ApfoJnCJXgg41ouw4wDHEKGCj42NceptkoaGBq6uri4oI8+4mDC21MvLCwdw+h7o9/f3HD6L5FhGXwROn5fgQFVPeDr4fODqOLmyshJkC047hi22t+D4ArhEjwI+MjLC0ckdgBSOFroOltgSHFc2JmyKzhc22Ki1tTUI723izmtFRQVXWVnJ0TOK9fX1XIbGGCw8Zg40Ffz19TXniuXr64t7f3/n3t7eOB24ek2KO2zqMARsDFvABrhEjxNch65ecdBnab4lC44VonAEyJ+ZwAEEVBqzcZLC+zVh4LRB5UbG9OjeiTxiTeAUhji8fQzwqqoqDvfAGxsbs21tbVxGHp63t7ccnRSpx8dHjqCfn5+5p6cnDhuDQCm8h4gN8PPzw9H39O8pTA8bQIWNGxwvNeHn6nBlApfoJQFOezodEfg7FRx7OuZn2gA6cISVxdAhwXHSUt8gcwHHn3F00LkO09vc3ORwtGBZmpqaOFwG0o6BEyquuv4Bbij46rTk9coAAAAASUVORK5CYII=&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_9be1b876fc220c2b22bc75a44a970ca5 = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_d1707f042f199bc480911a410925d0ed = L.imageOverlay(
                &quot;data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAFwAAAAwCAYAAACc2R15AAABtUlEQVR42u3ZzYqDQAzA8bz/M/UderIexFIQxC+wFylU6C4OzKJbK3ZM4mTM4X+SufyETGDgdDr9SK4sS7TqunYqy7LVgWK7Y38DLR58T2wXaAV3wN4CLRqcGxsDWiw4JzYmtEhwF9iqqmbjhhYHjon9CZwS+hDgc6OjaRrT/X6fxIEtAhx7Vs+Bc2F7D459Ob5er7c4sb0Gx9pGLpfLJAUnxO77/g38er2a0jQ1HR58C+5cCo6M/QnaFkXRpDiOTUmSmG6321+HAaeAHhq2kTH2+XxeBOdAB2nYz+dzscfjYbLr3xjbtgROjQ4SsLuuW13btiZ71s7sMfjQEjglOviK/Q2yC7jtEzgVOvgE7YpMBU6BDntiYwBvBedGB25sCuSt4JzoLODUyBjgXOgQCjQHOAY6hAKNAc6BDiFhY4BTo0Mo0NzgrugQEjYWOCU6hAI9B57nucm++Nhv34aJDtKhLepSW8HX/BAycN9x9wBfyhlcCq5v4P8DH6AxcUWDS8QVCS4ZVxR4CLhiwEPB9R48NFxR4CHgeg8eKrKC71hRFCYFV3AFV3AFV3AFV3AFV3BPwH8B7rUxKEpomD0AAAAASUVORK5CYII=&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_047cf7a4389ddf495e7be954fc800a97 = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_35a70477cdb1aec56ce558f46abbb54b = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_aa3f48d500ceccff387b5b78900cb83f = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_6610ffc3843fc286b5f922d0ff999b64 = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var image_overlay_ba0f0d14f5a326f6d0321ceb4226f921 = L.imageOverlay(
                &quot;data:image/png;base64,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&quot;,
                [[43.60734015888762, 3.767730367838], [43.81775157192726, 4.3423021816889245]],
                {&quot;crossOrigin&quot;: false, &quot;interactive&quot;: false, &quot;opacity&quot;: 0.7, &quot;zindex&quot;: 1}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);


            var layer_control_16772d432ce6954add8c65224c2554ae = {
                base_layers : {
                    &quot;openstreetmap&quot; : tile_layer_8ebb7bdf73674872bc467e44a609f99a,
                },
                overlays :  {
                    &quot;January&quot; : image_overlay_06ec3d8b1ee2472e0dee5d2551cc2776,
                    &quot;February&quot; : image_overlay_a89a77ffe85cc350b6d5e694476e9cb5,
                    &quot;March&quot; : image_overlay_9be1b876fc220c2b22bc75a44a970ca5,
                    &quot;April&quot; : image_overlay_d1707f042f199bc480911a410925d0ed,
                    &quot;May&quot; : image_overlay_047cf7a4389ddf495e7be954fc800a97,
                    &quot;June&quot; : image_overlay_35a70477cdb1aec56ce558f46abbb54b,
                    &quot;July&quot; : image_overlay_aa3f48d500ceccff387b5b78900cb83f,
                    &quot;August&quot; : image_overlay_6610ffc3843fc286b5f922d0ff999b64,
                    &quot;September&quot; : image_overlay_ba0f0d14f5a326f6d0321ceb4226f921,
                },
            };
            L.control.layers(
                layer_control_16772d432ce6954add8c65224c2554ae.base_layers,
                layer_control_16772d432ce6954add8c65224c2554ae.overlays,
                {&quot;autoZIndex&quot;: true, &quot;collapsed&quot;: true, &quot;position&quot;: &quot;topright&quot;}
            ).addTo(map_c411caddeadbebf70f7cebfd52330cd5);

&lt;/script&gt;
&lt;/html&gt;" width="700" height="500"style="border:none !important;" "allowfullscreen" "webkitallowfullscreen" "mozallowfullscreen"></iframe>



# 6. Plot temporal series for the data

# 6.1. We calculate the mean temperature per month


```python
data_interpolated
```




<div><svg style="position: absolute; width: 0; height: 0; overflow: hidden">
<defs>
<symbol id="icon-database" viewBox="0 0 32 32">
<path d="M16 0c-8.837 0-16 2.239-16 5v4c0 2.761 7.163 5 16 5s16-2.239 16-5v-4c0-2.761-7.163-5-16-5z"></path>
<path d="M16 17c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
<path d="M16 26c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
</symbol>
<symbol id="icon-file-text2" viewBox="0 0 32 32">
<path d="M28.681 7.159c-0.694-0.947-1.662-2.053-2.724-3.116s-2.169-2.030-3.116-2.724c-1.612-1.182-2.393-1.319-2.841-1.319h-15.5c-1.378 0-2.5 1.121-2.5 2.5v27c0 1.378 1.122 2.5 2.5 2.5h23c1.378 0 2.5-1.122 2.5-2.5v-19.5c0-0.448-0.137-1.23-1.319-2.841zM24.543 5.457c0.959 0.959 1.712 1.825 2.268 2.543h-4.811v-4.811c0.718 0.556 1.584 1.309 2.543 2.268zM28 29.5c0 0.271-0.229 0.5-0.5 0.5h-23c-0.271 0-0.5-0.229-0.5-0.5v-27c0-0.271 0.229-0.5 0.5-0.5 0 0 15.499-0 15.5 0v7c0 0.552 0.448 1 1 1h7v19.5z"></path>
<path d="M23 26h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 22h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 18h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
</symbol>
</defs>
</svg>
<style>/* CSS stylesheet for displaying xarray objects in jupyterlab.
 *
 */

:root {
  --xr-font-color0: var(--jp-content-font-color0, rgba(0, 0, 0, 1));
  --xr-font-color2: var(--jp-content-font-color2, rgba(0, 0, 0, 0.54));
  --xr-font-color3: var(--jp-content-font-color3, rgba(0, 0, 0, 0.38));
  --xr-border-color: var(--jp-border-color2, #e0e0e0);
  --xr-disabled-color: var(--jp-layout-color3, #bdbdbd);
  --xr-background-color: var(--jp-layout-color0, white);
  --xr-background-color-row-even: var(--jp-layout-color1, white);
  --xr-background-color-row-odd: var(--jp-layout-color2, #eeeeee);
}

html[theme=dark],
body[data-theme=dark],
body.vscode-dark {
  --xr-font-color0: rgba(255, 255, 255, 1);
  --xr-font-color2: rgba(255, 255, 255, 0.54);
  --xr-font-color3: rgba(255, 255, 255, 0.38);
  --xr-border-color: #1F1F1F;
  --xr-disabled-color: #515151;
  --xr-background-color: #111111;
  --xr-background-color-row-even: #111111;
  --xr-background-color-row-odd: #313131;
}

.xr-wrap {
  display: block !important;
  min-width: 300px;
  max-width: 700px;
}

.xr-text-repr-fallback {
  /* fallback to plain text repr when CSS is not injected (untrusted notebook) */
  display: none;
}

.xr-header {
  padding-top: 6px;
  padding-bottom: 6px;
  margin-bottom: 4px;
  border-bottom: solid 1px var(--xr-border-color);
}

.xr-header > div,
.xr-header > ul {
  display: inline;
  margin-top: 0;
  margin-bottom: 0;
}

.xr-obj-type,
.xr-array-name {
  margin-left: 2px;
  margin-right: 10px;
}

.xr-obj-type {
  color: var(--xr-font-color2);
}

.xr-sections {
  padding-left: 0 !important;
  display: grid;
  grid-template-columns: 150px auto auto 1fr 20px 20px;
}

.xr-section-item {
  display: contents;
}

.xr-section-item input {
  display: none;
}

.xr-section-item input + label {
  color: var(--xr-disabled-color);
}

.xr-section-item input:enabled + label {
  cursor: pointer;
  color: var(--xr-font-color2);
}

.xr-section-item input:enabled + label:hover {
  color: var(--xr-font-color0);
}

.xr-section-summary {
  grid-column: 1;
  color: var(--xr-font-color2);
  font-weight: 500;
}

.xr-section-summary > span {
  display: inline-block;
  padding-left: 0.5em;
}

.xr-section-summary-in:disabled + label {
  color: var(--xr-font-color2);
}

.xr-section-summary-in + label:before {
  display: inline-block;
  content: '►';
  font-size: 11px;
  width: 15px;
  text-align: center;
}

.xr-section-summary-in:disabled + label:before {
  color: var(--xr-disabled-color);
}

.xr-section-summary-in:checked + label:before {
  content: '▼';
}

.xr-section-summary-in:checked + label > span {
  display: none;
}

.xr-section-summary,
.xr-section-inline-details {
  padding-top: 4px;
  padding-bottom: 4px;
}

.xr-section-inline-details {
  grid-column: 2 / -1;
}

.xr-section-details {
  display: none;
  grid-column: 1 / -1;
  margin-bottom: 5px;
}

.xr-section-summary-in:checked ~ .xr-section-details {
  display: contents;
}

.xr-array-wrap {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 20px auto;
}

.xr-array-wrap > label {
  grid-column: 1;
  vertical-align: top;
}

.xr-preview {
  color: var(--xr-font-color3);
}

.xr-array-preview,
.xr-array-data {
  padding: 0 5px !important;
  grid-column: 2;
}

.xr-array-data,
.xr-array-in:checked ~ .xr-array-preview {
  display: none;
}

.xr-array-in:checked ~ .xr-array-data,
.xr-array-preview {
  display: inline-block;
}

.xr-dim-list {
  display: inline-block !important;
  list-style: none;
  padding: 0 !important;
  margin: 0;
}

.xr-dim-list li {
  display: inline-block;
  padding: 0;
  margin: 0;
}

.xr-dim-list:before {
  content: '(';
}

.xr-dim-list:after {
  content: ')';
}

.xr-dim-list li:not(:last-child):after {
  content: ',';
  padding-right: 5px;
}

.xr-has-index {
  font-weight: bold;
}

.xr-var-list,
.xr-var-item {
  display: contents;
}

.xr-var-item > div,
.xr-var-item label,
.xr-var-item > .xr-var-name span {
  background-color: var(--xr-background-color-row-even);
  margin-bottom: 0;
}

.xr-var-item > .xr-var-name:hover span {
  padding-right: 5px;
}

.xr-var-list > li:nth-child(odd) > div,
.xr-var-list > li:nth-child(odd) > label,
.xr-var-list > li:nth-child(odd) > .xr-var-name span {
  background-color: var(--xr-background-color-row-odd);
}

.xr-var-name {
  grid-column: 1;
}

.xr-var-dims {
  grid-column: 2;
}

.xr-var-dtype {
  grid-column: 3;
  text-align: right;
  color: var(--xr-font-color2);
}

.xr-var-preview {
  grid-column: 4;
}

.xr-index-preview {
  grid-column: 2 / 5;
  color: var(--xr-font-color2);
}

.xr-var-name,
.xr-var-dims,
.xr-var-dtype,
.xr-preview,
.xr-attrs dt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding-right: 10px;
}

.xr-var-name:hover,
.xr-var-dims:hover,
.xr-var-dtype:hover,
.xr-attrs dt:hover {
  overflow: visible;
  width: auto;
  z-index: 1;
}

.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  display: none;
  background-color: var(--xr-background-color) !important;
  padding-bottom: 5px !important;
}

.xr-var-attrs-in:checked ~ .xr-var-attrs,
.xr-var-data-in:checked ~ .xr-var-data,
.xr-index-data-in:checked ~ .xr-index-data {
  display: block;
}

.xr-var-data > table {
  float: right;
}

.xr-var-name span,
.xr-var-data,
.xr-index-name div,
.xr-index-data,
.xr-attrs {
  padding-left: 25px !important;
}

.xr-attrs,
.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  grid-column: 1 / -1;
}

dl.xr-attrs {
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 125px auto;
}

.xr-attrs dt,
.xr-attrs dd {
  padding: 0;
  margin: 0;
  float: left;
  padding-right: 10px;
  width: auto;
}

.xr-attrs dt {
  font-weight: normal;
  grid-column: 1;
}

.xr-attrs dt:hover span {
  display: inline-block;
  background: var(--xr-background-color);
  padding-right: 10px;
}

.xr-attrs dd {
  grid-column: 2;
  white-space: pre-wrap;
  word-break: break-all;
}

.xr-icon-database,
.xr-icon-file-text2,
.xr-no-icon {
  display: inline-block;
  vertical-align: middle;
  width: 1em;
  height: 1.5em !important;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
}
</style><pre class='xr-text-repr-fallback'>&lt;xarray.DataArray &#x27;LST_Day_1km&#x27; (time: 12, y: 48, x: 92)&gt;
array([[[   9.69,    9.69,    9.69, ...,   16.55,   16.71,   16.71],
        [   9.69,    9.69,    9.69, ...,   16.55,   16.71,   16.71],
        [   9.69,    9.69,    9.69, ...,   16.73,   16.69,   16.69],
        ...,
        [  15.51,   15.21,   15.21, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   13.47,   14.21,   14.21]],

       [[  12.93,   12.93,   12.93, ...,   17.77,   20.53,   20.53],
        [  12.93,   12.93,   12.93, ...,   20.53,   20.53,   20.53],
        [  12.93,   12.93,   12.93, ...,   20.53,   20.53,   20.53],
        ...,
        [  16.67,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [  17.83,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [  17.83,   17.83,   17.83, ...,   15.63,   15.97,   15.97]],

       [[  23.83,   23.27,   23.27, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.13,   30.55,   30.55],
        ...,
...
        ...,
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15]],

       [[   8.65,    8.65,    8.65, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.55,    9.47,    9.47],
        ...,
        [  10.17,   10.23,   10.23, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.91,    7.67,    7.67]],

       [[  16.45,   16.07,   16.07, ...,   13.49,   13.49,   13.49],
        [  16.59,   16.17,   16.17, ...,   13.49,   13.49,   13.49],
        [  16.59,   16.17,   16.17, ...,   13.49,   13.49,   13.49],
        ...,
        [  16.65,   16.99,   16.99, ...,   12.91,   12.91,   12.91],
        [  16.63,   16.79,   16.79, ...,   12.91,   12.91,   12.91],
        [  16.63,   16.79,   16.79, ...,   12.91,   12.91,   12.91]]])
Coordinates:
  * y            (y) float64 6.303e+06 6.302e+06 ... 6.28e+06 6.279e+06
  * x            (x) float64 7.622e+05 7.628e+05 ... 8.072e+05 8.078e+05
  * time         (time) datetime64[ns] 2021-01-31 2021-02-28 ... 2021-12-31
    spatial_ref  int64 0
Attributes:
    _FillValue:  -273.15</pre><div class='xr-wrap' style='display:none'><div class='xr-header'><div class='xr-obj-type'>xarray.DataArray</div><div class='xr-array-name'>'LST_Day_1km'</div><ul class='xr-dim-list'><li><span class='xr-has-index'>time</span>: 12</li><li><span class='xr-has-index'>y</span>: 48</li><li><span class='xr-has-index'>x</span>: 92</li></ul></div><ul class='xr-sections'><li class='xr-section-item'><div class='xr-array-wrap'><input id='section-5ffc6364-46ed-4912-92e4-bb1ecb20a026' class='xr-array-in' type='checkbox' checked><label for='section-5ffc6364-46ed-4912-92e4-bb1ecb20a026' title='Show/hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-array-preview xr-preview'><span>9.69 9.69 9.69 9.69 9.69 9.69 ... 12.91 12.91 12.91 12.91 12.91 12.91</span></div><div class='xr-array-data'><pre>array([[[   9.69,    9.69,    9.69, ...,   16.55,   16.71,   16.71],
        [   9.69,    9.69,    9.69, ...,   16.55,   16.71,   16.71],
        [   9.69,    9.69,    9.69, ...,   16.73,   16.69,   16.69],
        ...,
        [  15.51,   15.21,   15.21, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   11.71,   11.35,   11.35],
        [  15.47,   15.55,   15.55, ...,   13.47,   14.21,   14.21]],

       [[  12.93,   12.93,   12.93, ...,   17.77,   20.53,   20.53],
        [  12.93,   12.93,   12.93, ...,   20.53,   20.53,   20.53],
        [  12.93,   12.93,   12.93, ...,   20.53,   20.53,   20.53],
        ...,
        [  16.67,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [  17.83,   17.83,   17.83, ...,   13.59,   14.23,   14.23],
        [  17.83,   17.83,   17.83, ...,   15.63,   15.97,   15.97]],

       [[  23.83,   23.27,   23.27, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.33,   31.65,   31.65],
        [  24.17,   23.93,   23.93, ...,   31.13,   30.55,   30.55],
        ...,
...
        ...,
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15],
        [-273.15, -273.15, -273.15, ..., -273.15, -273.15, -273.15]],

       [[   8.65,    8.65,    8.65, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.33,    9.35,    9.35],
        [   8.63,    8.63,    8.63, ...,    9.55,    9.47,    9.47],
        ...,
        [  10.17,   10.23,   10.23, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.01,    6.09,    6.09],
        [  10.21,   10.17,   10.17, ...,    6.91,    7.67,    7.67]],

       [[  16.45,   16.07,   16.07, ...,   13.49,   13.49,   13.49],
        [  16.59,   16.17,   16.17, ...,   13.49,   13.49,   13.49],
        [  16.59,   16.17,   16.17, ...,   13.49,   13.49,   13.49],
        ...,
        [  16.65,   16.99,   16.99, ...,   12.91,   12.91,   12.91],
        [  16.63,   16.79,   16.79, ...,   12.91,   12.91,   12.91],
        [  16.63,   16.79,   16.79, ...,   12.91,   12.91,   12.91]]])</pre></div></div></li><li class='xr-section-item'><input id='section-86d1ef18-cdb1-4242-bf7a-10a7029189eb' class='xr-section-summary-in' type='checkbox'  checked><label for='section-86d1ef18-cdb1-4242-bf7a-10a7029189eb' class='xr-section-summary' >Coordinates: <span>(4)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>y</span></div><div class='xr-var-dims'>(y)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>6.303e+06 6.302e+06 ... 6.279e+06</div><input id='attrs-e5ffa459-06fe-4651-9a3b-8f96c1dd27a3' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-e5ffa459-06fe-4651-9a3b-8f96c1dd27a3' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-cc9245ac-33ca-46fc-9a78-fcae9f2a8a2d' class='xr-var-data-in' type='checkbox'><label for='data-cc9245ac-33ca-46fc-9a78-fcae9f2a8a2d' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>units :</span></dt><dd>metre</dd><dt><span>resolution :</span></dt><dd>-500.0</dd><dt><span>crs :</span></dt><dd>EPSG:2154</dd><dt><span>axis :</span></dt><dd>Y</dd><dt><span>long_name :</span></dt><dd>y coordinate of projection</dd><dt><span>standard_name :</span></dt><dd>projection_y_coordinate</dd></dl></div><div class='xr-var-data'><pre>array([6302750., 6302250., 6301750., 6301250., 6300750., 6300250., 6299750.,
       6299250., 6298750., 6298250., 6297750., 6297250., 6296750., 6296250.,
       6295750., 6295250., 6294750., 6294250., 6293750., 6293250., 6292750.,
       6292250., 6291750., 6291250., 6290750., 6290250., 6289750., 6289250.,
       6288750., 6288250., 6287750., 6287250., 6286750., 6286250., 6285750.,
       6285250., 6284750., 6284250., 6283750., 6283250., 6282750., 6282250.,
       6281750., 6281250., 6280750., 6280250., 6279750., 6279250.])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>x</span></div><div class='xr-var-dims'>(x)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>7.622e+05 7.628e+05 ... 8.078e+05</div><input id='attrs-b790cc44-73c7-41aa-9712-a9a485669c6d' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-b790cc44-73c7-41aa-9712-a9a485669c6d' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-f204979d-16e6-42ea-8326-c1c209e71fdf' class='xr-var-data-in' type='checkbox'><label for='data-f204979d-16e6-42ea-8326-c1c209e71fdf' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>units :</span></dt><dd>metre</dd><dt><span>resolution :</span></dt><dd>500.0</dd><dt><span>crs :</span></dt><dd>EPSG:2154</dd><dt><span>axis :</span></dt><dd>X</dd><dt><span>long_name :</span></dt><dd>x coordinate of projection</dd><dt><span>standard_name :</span></dt><dd>projection_x_coordinate</dd></dl></div><div class='xr-var-data'><pre>array([762250., 762750., 763250., 763750., 764250., 764750., 765250., 765750.,
       766250., 766750., 767250., 767750., 768250., 768750., 769250., 769750.,
       770250., 770750., 771250., 771750., 772250., 772750., 773250., 773750.,
       774250., 774750., 775250., 775750., 776250., 776750., 777250., 777750.,
       778250., 778750., 779250., 779750., 780250., 780750., 781250., 781750.,
       782250., 782750., 783250., 783750., 784250., 784750., 785250., 785750.,
       786250., 786750., 787250., 787750., 788250., 788750., 789250., 789750.,
       790250., 790750., 791250., 791750., 792250., 792750., 793250., 793750.,
       794250., 794750., 795250., 795750., 796250., 796750., 797250., 797750.,
       798250., 798750., 799250., 799750., 800250., 800750., 801250., 801750.,
       802250., 802750., 803250., 803750., 804250., 804750., 805250., 805750.,
       806250., 806750., 807250., 807750.])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>time</span></div><div class='xr-var-dims'>(time)</div><div class='xr-var-dtype'>datetime64[ns]</div><div class='xr-var-preview xr-preview'>2021-01-31 ... 2021-12-31</div><input id='attrs-709dc640-e74b-4576-9d02-9a9d6c45eef1' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-709dc640-e74b-4576-9d02-9a9d6c45eef1' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-e5fab1f4-d5cf-40f9-b76b-b9dcb751638f' class='xr-var-data-in' type='checkbox'><label for='data-e5fab1f4-d5cf-40f9-b76b-b9dcb751638f' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([&#x27;2021-01-31T00:00:00.000000000&#x27;, &#x27;2021-02-28T00:00:00.000000000&#x27;,
       &#x27;2021-03-31T00:00:00.000000000&#x27;, &#x27;2021-04-30T00:00:00.000000000&#x27;,
       &#x27;2021-05-31T00:00:00.000000000&#x27;, &#x27;2021-06-30T00:00:00.000000000&#x27;,
       &#x27;2021-07-31T00:00:00.000000000&#x27;, &#x27;2021-08-31T00:00:00.000000000&#x27;,
       &#x27;2021-09-30T00:00:00.000000000&#x27;, &#x27;2021-10-31T00:00:00.000000000&#x27;,
       &#x27;2021-11-30T00:00:00.000000000&#x27;, &#x27;2021-12-31T00:00:00.000000000&#x27;],
      dtype=&#x27;datetime64[ns]&#x27;)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span>spatial_ref</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>0</div><input id='attrs-cef2c17a-9d2e-45d6-a7f5-f76bdafea837' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-cef2c17a-9d2e-45d6-a7f5-f76bdafea837' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-79c8c91c-099e-49c9-9207-06c810fc6bbb' class='xr-var-data-in' type='checkbox'><label for='data-79c8c91c-099e-49c9-9207-06c810fc6bbb' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>crs_wkt :</span></dt><dd>PROJCS[&quot;RGF93 v1 / Lambert-93&quot;,GEOGCS[&quot;RGF93 v1&quot;,DATUM[&quot;Reseau_Geodesique_Francais_1993_v1&quot;,SPHEROID[&quot;GRS 1980&quot;,6378137,298.257222101]],PRIMEM[&quot;Greenwich&quot;,0],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;4171&quot;]],PROJECTION[&quot;Lambert_Conformal_Conic_2SP&quot;],PARAMETER[&quot;latitude_of_origin&quot;,46.5],PARAMETER[&quot;central_meridian&quot;,3],PARAMETER[&quot;standard_parallel_1&quot;,49],PARAMETER[&quot;standard_parallel_2&quot;,44],PARAMETER[&quot;false_easting&quot;,700000],PARAMETER[&quot;false_northing&quot;,6600000],UNIT[&quot;metre&quot;,1],AXIS[&quot;Easting&quot;,EAST],AXIS[&quot;Northing&quot;,NORTH],AUTHORITY[&quot;EPSG&quot;,&quot;2154&quot;]]</dd><dt><span>semi_major_axis :</span></dt><dd>6378137.0</dd><dt><span>semi_minor_axis :</span></dt><dd>6356752.314140356</dd><dt><span>inverse_flattening :</span></dt><dd>298.257222101</dd><dt><span>reference_ellipsoid_name :</span></dt><dd>GRS 1980</dd><dt><span>longitude_of_prime_meridian :</span></dt><dd>0.0</dd><dt><span>prime_meridian_name :</span></dt><dd>Greenwich</dd><dt><span>geographic_crs_name :</span></dt><dd>RGF93 v1</dd><dt><span>horizontal_datum_name :</span></dt><dd>Reseau Geodesique Francais 1993 v1</dd><dt><span>projected_crs_name :</span></dt><dd>RGF93 v1 / Lambert-93</dd><dt><span>grid_mapping_name :</span></dt><dd>lambert_conformal_conic</dd><dt><span>standard_parallel :</span></dt><dd>(49.0, 44.0)</dd><dt><span>latitude_of_projection_origin :</span></dt><dd>46.5</dd><dt><span>longitude_of_central_meridian :</span></dt><dd>3.0</dd><dt><span>false_easting :</span></dt><dd>700000.0</dd><dt><span>false_northing :</span></dt><dd>6600000.0</dd><dt><span>spatial_ref :</span></dt><dd>PROJCS[&quot;RGF93 v1 / Lambert-93&quot;,GEOGCS[&quot;RGF93 v1&quot;,DATUM[&quot;Reseau_Geodesique_Francais_1993_v1&quot;,SPHEROID[&quot;GRS 1980&quot;,6378137,298.257222101]],PRIMEM[&quot;Greenwich&quot;,0],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;4171&quot;]],PROJECTION[&quot;Lambert_Conformal_Conic_2SP&quot;],PARAMETER[&quot;latitude_of_origin&quot;,46.5],PARAMETER[&quot;central_meridian&quot;,3],PARAMETER[&quot;standard_parallel_1&quot;,49],PARAMETER[&quot;standard_parallel_2&quot;,44],PARAMETER[&quot;false_easting&quot;,700000],PARAMETER[&quot;false_northing&quot;,6600000],UNIT[&quot;metre&quot;,1],AXIS[&quot;Easting&quot;,EAST],AXIS[&quot;Northing&quot;,NORTH],AUTHORITY[&quot;EPSG&quot;,&quot;2154&quot;]]</dd><dt><span>GeoTransform :</span></dt><dd>762000.0 500.0 0.0 6303000.0 0.0 -500.0</dd></dl></div><div class='xr-var-data'><pre>array(0)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-91c2fe80-b29c-496a-b042-402f78172bc2' class='xr-section-summary-in' type='checkbox'  ><label for='section-91c2fe80-b29c-496a-b042-402f78172bc2' class='xr-section-summary' >Indexes: <span>(3)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-index-name'><div>y</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-986f425e-1233-461f-9802-78be717bea80' class='xr-index-data-in' type='checkbox'/><label for='index-986f425e-1233-461f-9802-78be717bea80' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([6302750.0, 6302250.0, 6301750.0, 6301250.0, 6300750.0, 6300250.0,
       6299750.0, 6299250.0, 6298750.0, 6298250.0, 6297750.0, 6297250.0,
       6296750.0, 6296250.0, 6295750.0, 6295250.0, 6294750.0, 6294250.0,
       6293750.0, 6293250.0, 6292750.0, 6292250.0, 6291750.0, 6291250.0,
       6290750.0, 6290250.0, 6289750.0, 6289250.0, 6288750.0, 6288250.0,
       6287750.0, 6287250.0, 6286750.0, 6286250.0, 6285750.0, 6285250.0,
       6284750.0, 6284250.0, 6283750.0, 6283250.0, 6282750.0, 6282250.0,
       6281750.0, 6281250.0, 6280750.0, 6280250.0, 6279750.0, 6279250.0],
      dtype=&#x27;float64&#x27;, name=&#x27;y&#x27;))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>x</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-2f2dd7aa-0648-4441-b698-ad11bf039393' class='xr-index-data-in' type='checkbox'/><label for='index-2f2dd7aa-0648-4441-b698-ad11bf039393' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([762250.0, 762750.0, 763250.0, 763750.0, 764250.0, 764750.0, 765250.0,
       765750.0, 766250.0, 766750.0, 767250.0, 767750.0, 768250.0, 768750.0,
       769250.0, 769750.0, 770250.0, 770750.0, 771250.0, 771750.0, 772250.0,
       772750.0, 773250.0, 773750.0, 774250.0, 774750.0, 775250.0, 775750.0,
       776250.0, 776750.0, 777250.0, 777750.0, 778250.0, 778750.0, 779250.0,
       779750.0, 780250.0, 780750.0, 781250.0, 781750.0, 782250.0, 782750.0,
       783250.0, 783750.0, 784250.0, 784750.0, 785250.0, 785750.0, 786250.0,
       786750.0, 787250.0, 787750.0, 788250.0, 788750.0, 789250.0, 789750.0,
       790250.0, 790750.0, 791250.0, 791750.0, 792250.0, 792750.0, 793250.0,
       793750.0, 794250.0, 794750.0, 795250.0, 795750.0, 796250.0, 796750.0,
       797250.0, 797750.0, 798250.0, 798750.0, 799250.0, 799750.0, 800250.0,
       800750.0, 801250.0, 801750.0, 802250.0, 802750.0, 803250.0, 803750.0,
       804250.0, 804750.0, 805250.0, 805750.0, 806250.0, 806750.0, 807250.0,
       807750.0],
      dtype=&#x27;float64&#x27;, name=&#x27;x&#x27;))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>time</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-64a7dab7-85b1-40a8-87c5-29de687b0def' class='xr-index-data-in' type='checkbox'/><label for='index-64a7dab7-85b1-40a8-87c5-29de687b0def' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(DatetimeIndex([&#x27;2021-01-31&#x27;, &#x27;2021-02-28&#x27;, &#x27;2021-03-31&#x27;, &#x27;2021-04-30&#x27;,
               &#x27;2021-05-31&#x27;, &#x27;2021-06-30&#x27;, &#x27;2021-07-31&#x27;, &#x27;2021-08-31&#x27;,
               &#x27;2021-09-30&#x27;, &#x27;2021-10-31&#x27;, &#x27;2021-11-30&#x27;, &#x27;2021-12-31&#x27;],
              dtype=&#x27;datetime64[ns]&#x27;, name=&#x27;time&#x27;, freq=None))</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-eda8e197-408c-45c1-b3fe-a8f23e4d8bb4' class='xr-section-summary-in' type='checkbox'  checked><label for='section-eda8e197-408c-45c1-b3fe-a8f23e4d8bb4' class='xr-section-summary' >Attributes: <span>(1)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><dl class='xr-attrs'><dt><span>_FillValue :</span></dt><dd>-273.15</dd></dl></div></li></ul></div></div>



To plot temporal series of the temperature, we must calculate the averaage of temperatures per month in each raster. However, we need to deal with the nodata values first, since a high negative value can influence the mean value.


```python
# Remove data where we have absolute kelvin -273.15. This will add np.nan where temperatures are -273.15
data_with_nan = [data_month.data for data_month in data_interpolated.where(data != -273.15)]

# Remove np.nan values
data_without_nan = [data_month[~np.isnan(data_month)] for data_month in data_with_nan]

# Calculate the mean with the present values
data_temp_mean = [np.nanmean(month_data) for month_data in data_without_nan ]

data_temp_mean
```




    [11.963057065217424,
     16.11516304347829,
     26.789483695652205,
     19.04933423913047,
     29.070221920289885,
     33.015901268115975,
     39.149814311594234,
     35.1516077898551,
     19.180919384058,
     -273.1500000000001,
     9.299877717391333,
     14.698691123188437]



The month of October has nodata values only. Lets replace it by the average of the temperatures of September and November


```python
data_temp_mean[9] = np.mean( np.array([ data_temp_mean[8], data_temp_mean[10] ]), axis=0 )
data_temp_mean
```




    [11.963057065217424,
     16.11516304347829,
     26.789483695652205,
     19.04933423913047,
     29.070221920289885,
     33.015901268115975,
     39.149814311594234,
     35.1516077898551,
     19.180919384058,
     14.240398550724667,
     9.299877717391333,
     14.698691123188437]



We create a dataframe with the columns temperature and month


```python
import pandas as pd
df_temp = pd.DataFrame({'month': list(months.keys()), 'temperature':data_temp_mean})
df_temp
```





  <div id="df-2af38853-3db3-48c8-838c-5474f0ad1fee" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>month</th>
      <th>temperature</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>January</td>
      <td>11.963057</td>
    </tr>
    <tr>
      <th>1</th>
      <td>February</td>
      <td>16.115163</td>
    </tr>
    <tr>
      <th>2</th>
      <td>March</td>
      <td>26.789484</td>
    </tr>
    <tr>
      <th>3</th>
      <td>April</td>
      <td>19.049334</td>
    </tr>
    <tr>
      <th>4</th>
      <td>May</td>
      <td>29.070222</td>
    </tr>
    <tr>
      <th>5</th>
      <td>June</td>
      <td>33.015901</td>
    </tr>
    <tr>
      <th>6</th>
      <td>July</td>
      <td>39.149814</td>
    </tr>
    <tr>
      <th>7</th>
      <td>August</td>
      <td>35.151608</td>
    </tr>
    <tr>
      <th>8</th>
      <td>September</td>
      <td>19.180919</td>
    </tr>
    <tr>
      <th>9</th>
      <td>October</td>
      <td>14.240399</td>
    </tr>
    <tr>
      <th>10</th>
      <td>november</td>
      <td>9.299878</td>
    </tr>
    <tr>
      <th>11</th>
      <td>December</td>
      <td>14.698691</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-2af38853-3db3-48c8-838c-5474f0ad1fee')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-2af38853-3db3-48c8-838c-5474f0ad1fee button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-2af38853-3db3-48c8-838c-5474f0ad1fee');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-b5fc9db9-dc33-447c-9dba-3509b616a66b">
  <button class="colab-df-quickchart" onclick="quickchart('df-b5fc9db9-dc33-447c-9dba-3509b616a66b')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-b5fc9db9-dc33-447c-9dba-3509b616a66b button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>
    </div>
  </div>




## 6.2. Plot data with seaborn


```python
import seaborn as sns
sns.set_theme(rc={'figure.figsize':(8.7,7.27)})
sns.lineplot(x="month", y="temperature", data=df_temp)
plt.xticks(rotation=15)
plt.title(f'Montpellier verage temp in {year}')
plt.show()
```


    
![png](output_36_0.png)
    


# 7. Make a animation from the output raster

## 7.1. Create folders


```python
print("Current Working Directory " , os.getcwd())
import matplotlib.pyplot as pyplot
in_dir = os.getcwd() + '/temp'
os.chdir(in_dir)
print("Input Directory " , in_dir)

```

    Current Working Directory  /content
    Input Directory  /content/temp



```python
out_dir = os.getcwd() + '/animation/'
print("Animation out dir",out_dir)
if not os.path.exists(out_dir): os.makedirs(out_dir)
file_list = [file for file in os.listdir() if file.endswith('.tiff')]
print(os.listdir())

```

    Animation out dir /content/temp/animation/
    ['modis_tile_10_.tiff', 'modis_tile_02_.tiff', 'modis_tile_03_.tiff', 'modis_tile_12_.tiff', 'modis_tile_05_.tiff', 'modis_tile_06_.tiff', 'modis_tile_11_.tiff', 'modis_tile_08_.tiff', 'animation', 'modis_tile_09_.tiff', 'modis_tile_04_.tiff', 'modis_tile_01_.tiff', 'modis_tile_07_.tiff']



```python
for f in file_list:
    raw_file = rasterio.open(f)
    file = raw_file.read(1)

    cmap='turbo'
    plt.figure(figsize=(12,15))
    plt.imshow(file,cmap=cmap,vmin=0,vmax=60)
    parts = f.split("_")
    file_name = parts[2]
    print(f'Processing: {file_name}')
    plt.title(f"Temperature {file_name}", fontsize=28,fontweight='bold')
    cur_axes = plt.gca()
    cur_axes.axes.get_xaxis().set_visible(False)
    cur_axes.axes.get_yaxis().set_visible(False)
    cb = plt.colorbar(fraction=0.046, pad=0.04,shrink=0.3)
    cb.set_label('Temperature time series', size=24)
    cb.ax.tick_params(labelsize=18)
    plt.tight_layout()
    print(out_dir)
    plt.savefig('{}{}_temperature.png'.format(out_dir,file_name,dpi=150))
    plt.close()

```

    Processing: 10
    /content/temp/animation/
    Processing: 02
    /content/temp/animation/
    Processing: 03
    /content/temp/animation/
    Processing: 12
    /content/temp/animation/
    Processing: 05
    /content/temp/animation/
    Processing: 06
    /content/temp/animation/
    Processing: 11
    /content/temp/animation/
    Processing: 08
    /content/temp/animation/
    Processing: 09
    /content/temp/animation/
    Processing: 04
    /content/temp/animation/
    Processing: 01
    /content/temp/animation/
    Processing: 07
    /content/temp/animation/


## 7.2. Create animation


```python
import imageio.v2 as imageio

def make_gif(input_folder, save_file_path):
    episode_frames = []
    time_per_step = 4
    for root, _, files in os.walk(input_folder):
        file_paths = [os.path.join(root,file) for file in files]
        file_paths = sorted (file_paths,key=lambda x:os.path.getmtime(x))
        episode_frames = [imageio.imread(file_path) for file_path in file_paths if file_path.endswith('.png')]
    episode_frames = np.array(episode_frames)
    imageio.mimsave(save_file_path,episode_frames,duration=6)

make_gif(out_dir,os.path.join(out_dir,"Animation_Temp.gif"))
```

## 7.3. Visualize animation


```python
from IPython.display import Image, display
with open(out_dir + "Animation_Temp.gif", 'rb') as f:
    display(Image(data=f.read(),format='png',width = 600, height = 600))
```


    
![png](output_45_0.png)
    



```python

```
