# 01 - 	On a QUEST (Query, Unify, Explore SpatioTemporal) to Accelerate ICESat-2 Applications in Ocean Science via icepyx

Authors: Jessica Scheick, Kelsey Bisson, Zachary Fair, Romina Piunno, Nicole Abib, Alessandro Di Bella, Rachel Tilling\
Presented by: Zachary Fair

## Abstract 
Collaboration and inclusivity will be critical for tackling modern environmental challenges resulting from increased anthropogenic stressors. Even as we celebrate 2023 as the Year of Open Source Science, many scientific disciplines - and thus their technologies, tools, and expertise - remain siloed. The ICESat-2 (Ice, Cloud, and land Elevation Satellite 2) was launched in 2018 with a strong focus towards cryospheric investigations. However, the sensor’s contributions to advances among the vegetation and oceanography communities are nontrivial. New environmental discoveries are made possible by exposing the mission and its unique capabilities to other disciplines.

The QUEST (Query, Unify, Explore SpatioTemporal) Python software library aims to bridge this divide. QUEST is a submodule of icepyx, a community of data managers, users, and developers and a software library providing tooling for ICESat-2 data access, visualization, processing, and analysis locally and in the cloud. The QUEST module capitalizes on the object-oriented properties of Python (and thus icepyx) to create a framework for easily integrating additional datasets into a single workflow. The motivating use case for developing QUEST came from oceanography and aims to combine ICESat-2 data with Argo data to investigate sea ice phytoplankton. Wanting to make their work as interoperable as possible, the development team created QUEST with a templated framework to enable easy addition of additional sensors and datasets. This presentation will showcase the QUEST module and the ways in which it can and is enabling oceanographic investigations.


## What is icepyx?

### What is QUEST?


## Datasets

### ICESat-2 Basics

### Argo Basics
Argo floats are untethered sensors that bob in the ocean to collect data as they are carried by ocean currents. Argo floats can control their own buoyancy but not their trajectory. They primarily measure salinity, temperature, and biological activity within the Earth's oceans, though some Argo floats also provide optical measurements (light attenuation at 490 nm, particulate backscatter at 700 nm) to validate satellite measurements.

A portion of Argo floats are situated near the poles, and they sometimes drift underneath sea ice. Ocean conditions beneath sea ice are not well understood, so polar Argo floats provide useful data for the oceanographic community.

## QUEST Example
```python
# Basic packages
import geopandas as gpd
import h5py
import matplotlib.pyplot as plt
import numpy as np
from os import listdir
from os.path import isfile, join
import pandas as pd
from pprint import pprint
import requests

# icepyx and QUEST
import icepyx as ipx
```

### Define the Query Object
To start looking for data, we begin by defining our Quest object. We provide the following bounding parameters:
* `spatial_extent`: Data is constrained to a bounding box over the Pacific Ocean.
* `date_range`: Only grab data from April 12-26, 2022.

```python
# Spatial bounds, given as SW/NE corners
spatial_extent = [-154, 30, -143, 37]

# Start and end dates, in YYYY-MM-DD format
date_range = ['2022-04-12', '2022-04-26']

# Initialize the QUEST object
reg_a = ipx.Quest(spatial_extent=spatial_extent, date_range=date_range)

print(reg_a)
```
![query](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/quest_query.PNG)

We have defined our spatial and temporal domains, now we need to add datasets to our query!

### Getting the ICESat-2 data
If we want to extract information about the water column, the **ICESat-2 ATL03 product** is likely the desired choice.\

Normally, the query would return 13 ICESat-2 files for downloading. This may be potentially useful for a regular user, but it is excessive for this example. Hence, we specify a reference ground track (RGT, `track`) below.

```python
# ICESat-2 product
short_name = 'ATL03'

# Reference ground track (RGT)
track = '411'

# Add ICESat-2 to QUEST query
reg_a.add_icesat2(product=short_name, tracks=track)
print(reg_a)
```
![query2](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/quest_query2.PNG)

We can now see the available ICESat-2 file over our region of interest.

```python
pprint(reg_a.datasets['icesat2'].avail_granules(ids=True))
```
![is2list](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/is2_file_list.PNG)

For more information on functions that can be used for ICESat-2, users are referred to the [icepyx ReadtheDocs](https://icepyx.readthedocs.io/en/latest/)

### Getting the Argo data
Accessing Argo data is similar to ICESat-2:

```python
reg_a.add_argo()
```

By default, Argo data is organized as vertical profiles of temperature as a function of pressure. The user can supply a list of desired parameters using the code below:

```python
# Customized variable query
reg_a.add_argo(params=['temperature'])
```

Additionally, a user may view or update the list of Argo parameters at any time:

```python
# Update the list of Argo parameters
reg_a.datasets['argo'].params = ['temperature', 'salinity']
```

Finally, let's add Argo to our wanted datasets!

```python
reg_a.datasets['argo'].search_data()
```
![argocount](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/argo_file_count.PNG)

### Downloading the data
We can now access data for both ICESat-2 and Argo! The below function will download both datasets at once.

```python
# Path for saved ICESat-2 files
path = '/icepyx/quest/downloaded-data/'

# Access ICESat-2 and Argo data simultaneously
reg_a.download_all()
```

**Important**: With our current code, the Argo data is compiled into a Pandas DataFrame, whereas the processed ICESat-2 data is saved as HDF-5 files to the directory given above.

We now have 13 Argo profiles, each containing `pressure`, `temperature`, and `salinity`!

If the user wishes to add more profiles to a pre-existing query, they should use the following syntax:

```python
reg_a.download_all(path, keep_existing=True)

# Set Aego data as its own DataFrame
argo_df = reg_a.datasets['argo'].argodata
```

### Reading and Visualizing the Data
We already have the Argo data ready in a Pandas DataFrame, but the ICESat-2 data is saved as several HDF-5 files. Because these data files are often large, we only focus on one granule for this example.

The below workflow uses the [icepyx Read module](https://icepyx.readthedocs.io/en/latest/example_notebooks/IS2_data_read-in.html) to quickly load ICESat-2 data into the Xarray format.

```python
file_path = f'{path}/quest-test-data/processed_ATL03_data.h5'
reader = ipx.Read(file_path)

# Specify the variables to be read
reader.vars.append(beam_list=['gt2l'],
                   var_list=['h_ph', 'lat_ph', 'lon_ph', 'signal_conf_ph'])

# Load ICESat-2 data into Xarray
ds = reader.load()
```
![is2xarray](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/is2_xarray_structure.PNG)

To make the data easier to plot, let's convert the data into a Pandas DataFrame, just like Argo.

```python
# Convert from Xarray to Pandas
is2_pd = ds.to_dataframe()

# Rearrange the data to only include "ocean" photons
is2_pd = is2-pd.reset_index(level=[0,1,2])
is2_pd_ocean = is2_pd[is2_pd.index==1]
```

To view the relative locations of ICESat-2 and Argo, the below code coverts the data into GeoDataFrames and uses the `explore()` function. Note that for large datasets like ICESat-2, loading the map might take a while.

```python
# Convert to GeodataFrames
is2_gdf = gpd.GeoDataFrame(is2_pd_ocean,
                           geometry=gpd.points_from_xy(is2_pd_ocean['lon_ph'], is2_pd_ocean['lat_ph']),
                           crs='EPSG:4326')
argo_gdf = gpd.GeoDataFrame(argo_df,
                           geometry=gpd.points_from_xy(argo_df['lon'], argo_df['lat']),
                           crs='EPSG:4326')

# Drop time variables (these cause errors with the "explore" function
is2_gdf = is2_gdf.drop(['data_start_utc', 'data_end_etc', 'delta_time', 'atlas_sdp_gps_epoch'], axis=1)

# Plot the ICESat-2 track (medium/high confidence photons only) on a map
m = is2_gdf[is2_gdf['signal_conf_ph']>=3].explore(column='rgt',
                                                  tiles='Esri.WorldImagery',
                                                  name='ICESat-2')

# Add Argo float locations to map
argo_gdf.explore(m=m, name='Argo', marker_kwds={'radius': 6}, color='red')
```
![exploremap](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/explore_map.PNG)

While we're at it, let's check out the temperature/pressure profiles for all of the Argo floats.

```python
# Plot vertical profile of temperature vs. pressure for all of the floats
fig, ax = plt.subplots(figsize=(12, 6))
for pid in np.unique(argo_df['profile_id']):
    argo_df[argo_df['profile_id']==pid].plot(ax=ax, x='temperature', y='pressure', label=pid)
plt.gca().invert_yaxis()
plt.xlabel('Temperature [$\degree$C]')
plt.ylabel('Pressure [hPa]')
plt.ylim([750, -10])
plt.tight_layout()
```
![argoprofile](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/argo_vertical_profile.PNG)

Lastly, let's look at some near-coincident ICESat-2 and Argo data in a multi-panel plot.

```python
# Only consider ICESat-2 signal photons
is2_pd_signal = is2_pd_ocean[is2_pd_ocean['signal_conf_ph']>=0]

## Multi-panel plot showing ICESat-2 and Argo data

# Calculate Extent
lons = [-154, -143, -143, -154, -154]
lats = [30, 30, 37, 37, 30]
lon_margin = (max(lons) - min(lons)) * 0.1
lat_margin = (max(lats) - min(lats)) * 0.1

# Create Plot
fig,([ax1,ax2],[ax3,ax4]) = plt.subplots(2, 2, figsize=(12, 6))

# Plot Relative Global View
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
world.plot(ax=ax1, color='0.8', edgecolor='black')
argo_df.plot.scatter(ax=ax1, x='lon', y='lat', s=25.0, c='green', zorder=3, alpha=0.3)
is2_pd_signal.plot.scatter(ax=ax1, x='lon_ph', y='lat_ph', s=10.0, zorder=2, alpha=0.3)
ax1.plot(lons, lats, linewidth=1.5, color='orange', zorder=2)
#df.plot(ax=ax2, x='lon', y='lat', marker='o', color='red', markersize=2.5, zorder=3)
ax1.set_xlim(-160,-100)
ax1.set_ylim(20,50)
ax1.set_aspect('equal', adjustable='box')
ax1.set_xlabel('Longitude', fontsize=18)
ax1.set_ylabel('Latitude', fontsize=18)

# Plot Zoomed View of Ground Tracks
argo_df.plot.scatter(ax=ax2, x='lon', y='lat', s=50.0, c='green', zorder=3, alpha=0.3)
is2_pd_signal.plot.scatter(ax=ax2, x='lon_ph', y='lat_ph', s=10.0, zorder=2, alpha=0.3)
ax2.plot(lons, lats, linewidth=1.5, color='orange', zorder=1)
ax2.scatter(-151.98956, 34.43885, color='orange', marker='^', s=80, zorder=4)
ax2.set_xlim(min(lons) - lon_margin, max(lons) + lon_margin)
ax2.set_ylim(min(lats) - lat_margin, max(lats) + lat_margin)
ax2.set_aspect('equal', adjustable='box')
ax2.set_xlabel('Longitude', fontsize=18)
ax2.set_ylabel('Latitude', fontsize=18)

# Plot ICESat-2 along-track vertical profile. A dotted line notes the location of a nearby Argo float
is2 = ax3.scatter(is2_pd_signal['lat_ph'], is2_pd_signal['h_ph']+13.1, s=0.1)
ax3.axvline(34.43885, linestyle='--', linewidth=3, color='black')
ax3.set_xlim([34.3, 34.5])
ax3.set_ylim([-20, 5])
ax3.set_xlabel('Latitude', fontsize=18)
ax3.set_ylabel('Approx. IS-2 Depth [m]', fontsize=16)
ax3.set_yticklabels(['15', '10', '5', '0', '-5'])

# Plot vertical ocean profile of the nearby Argo float
argo_df[argo_df['profile_id']=='4903409_053'].plot(ax=ax4, x='temperature', y='pressure', linewidth=3)
ax4.set_yscale('log')
ax4.invert_yaxis()
ax4.get_legend().remove()
ax4.set_xlabel('Temperature [$\degree$C]', fontsize=18)
ax4.set_ylabel('Argo Pressure', fontsize=16)

plt.tight_layout()
```
![fourpanel](https://github.com/zachghiaccio/jupyterbook-2023/blob/quest-presentation/speaker_content/01-morning-oral-session/files/bisson/four_panel_plot.PNG)

## Wrap-Up
In this notebook, we demonstrated that, thanks to icepyx and QUEST, it is easy to access ICESat-2 and icepyx data simultaneously. Because ICESat-2 and Argo both have the capabilities to offer near real-time, vertically-resolved subsurface information of the world's oceans, The QUEST module will be helpful for the continued monitoring of ocean biology and biogeochemistry.

Although we highlight Argo data in this presentation, QUEST has a framework designed to accept other datasets to compliment ICESat-2. We hope to expand the capabilities of QUEST with the help of the broader scientific community.