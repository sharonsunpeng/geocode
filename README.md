# geocode in python
create map for data with coordinates (latitude and longtitude) and data with boundary polygons


# There are three ways to create geometry of points from data frame file with two columns of latitude and longitude or one column of WKT
import geopandas as gpd
from shapely.geometry import Point
%matplotlib inline


# Get geodataframe of points
# Option 1 to create geometry of points
geometry = gpd.GeoSeries.from_wkt(pandas_df['WKT']) # pandas_df is a data frame object
# Option 2 to create geometry of points
geometry =[Point(xy) for xy in zip(pandas_df.longitude, pandas_df.latitude)]
# convert pandas data frame to geodataframe with transformed geometry
geodf = gpd.GeoDataFrame(pandas_df, crs="EPSG:4326", geometry=geometry)
# Option 3 Convert pandas data frame to geodataframe from latitude and longitude 
geodf=gpd.GeoDataFrame(pandas_df, geometry=gpd.points_from_xy(pandas_df.longitude, pandas_df.latitude), crs="EPSG:4326")


# Get geodataframe of polygons 
geometry2 = gpd.GeoSeries.from_wkt(pandas_df_polygon['WKT'])
gdf_polygon = gpd.GeoDataFrame(pandas_df_polygon, crs="EPSG:27200", geometry=geometry2)

# Create a map with both points and polygons 

# the problem I got once was the point geometry and the polygon geometry used different coordinate reference system, so pls check the crs of both geodataframes with the method: geodf.crs

# Convert geodataframe from one crs to another crs
geodf_crs_change=gpd.GeoDataFrame.to_crs(geodf,  crs="EPSG:27200")  # to find out more about crs and EPSG, go to: https://epsg.io/

# Create two layer map of polygons and points
from matplotlib import pyplot as plt # for plotting points with polygons on the same graph (multi-layers)
fig, ax = plt.subplots(figsize=(15,15))
geodf.plot(ax=ax,color="pink")
geodf_polygon.boundary.plot(ax=ax)
