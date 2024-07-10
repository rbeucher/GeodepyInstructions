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

with laspy.file.File("test.las", mode="r") as las_file:
    # Extract x, y, z data
    x_coords = las_file.x
    y_coords = las_file.y
    z_coords = las_file.z
    
    with open("coordinates_with_z.txt", "w") as coord_file:
        # Write header to file
        coord_file.write("Latitude,Longitude,Z\n")
        
        # Convert and write coordinates with z
        for x, y, z in zip(x_coords, y_coords, z_coords):
            lat, lon = convert_coordinates(x, y)
            coord_file.write(f"{lat},{lon},{z}\n")

```


Export to lasfile:

```python
import laspy
from osgeo import ogr, osr

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

# Read the input LAS file
input_file = "test.las"
output_file = "transformed_output.las"

with laspy.file.File(input_file, mode="r") as las_file:
    # Extract x, y, z data
    x_coords = las_file.x
    y_coords = las_file.y
    z_coords = las_file.z
    
    # Convert coordinates
    latitudes = []
    longitudes = []
    for x, y in zip(x_coords, y_coords):
        lat, lon = convert_coordinates(x, y)
        latitudes.append(lat)
        longitudes.append(lon)
        
    # Prepare the header for the output LAS file
    header = las_file.header.copy()
    
    # Create a new LAS file with transformed coordinates
    with laspy.file.File(output_file, mode="w", header=header) as out_las_file:
        out_las_file.X = las_file.X  # Copy original x coordinates (can be left unchanged or modified as needed)
        out_las_file.Y = las_file.Y  # Copy original y coordinates (can be left unchanged or modified as needed)
        out_las_file.Z = las_file.Z  # Copy original z coordinates
        out_las_file.Lat = latitudes  # Add transformed latitudes
        out_las_file.Lon = longitudes  # Add transformed longitudes

```

