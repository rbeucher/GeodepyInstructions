# Convert LAS file from GDA2020 MGA Zone 56 to lat/lon

## Installation instruction

1. **Open Anaconda Prompt**:
   - Click on the Start menu, search for "Anaconda Prompt," and open it.

2. **Create a New Conda Environment**:
   - Activate the geodepy_env environment:
     ```sh
     conda activate geodepy_env
     ```

   - Install `laspy`
     ```sh
     conda install -c conda-forge laspy
     ```   

## Example code to convert from GDA2020 MGA Zone 56 to lat/lon 

The EPSG code for GDA2020 MGA Zone 56 is EPSG:7856.

Here's the relevant information:

Datum: Geocentric Datum of Australia 2020 (GDA2020)
Coordinate System: Map Grid of Australia (MGA)
Zone: 56


```python
import laspy
from osgeo import ogr, osr

las = laspy.read('SW_222000_7404000_1k_class_Ellipsoid.las')

def convert_coordinates(x, y, input_epsg=7856, output_epsg=4326):
    # Create coordinate transformation
    input_spatial_ref = osr.SpatialReference()
    input_spatial_ref.ImportFromEPSG(input_epsg)

    output_spatial_ref = osr.SpatialReference()
    output_spatial_ref.ImportFromEPSG(output_epsg)

    coord_transform = osr.CoordinateTransformation(input_spatial_ref, output_spatial_ref)
    
    # Transform point
    lon, lat, _ = coord_transform.TransformPoint(x, y)
    return lat, lon

# Extract x, y, z data
x_coords = las_file.x
y_coords = las_file.y
z_coords = las_file.z

latitudes = []
longitudes = []

# Convert and write coordinates with z
for x, y in zip(x_coords, y_coords):
    lat, lon = convert_coordinates(x, y)
    latitudes.append(lat)
    longitudes.append(lon)

# Prepare the header for the output LAS file
header = las.header.copy()

# Create las file
new_file = laspy.create(file_version=header.version)
new_file.Lat = latitudes
new_file.Lon = longitudes
new_file.Z = z_coords

# Write to disk
new_file.write('extracted_points.las')

```


