
## Installation Instructions for GeodePy in a Conda Environment on Windows

### Prerequisites

1. **Install Anaconda or Miniconda**:
   - Download and install [Anaconda](https://www.anaconda.com/products/distribution) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html) from their respective websites.

### Step-by-Step Installation Guide

1. **Open Anaconda Prompt**:
   - Click on the Start menu, search for "Anaconda Prompt," and open it.

2. **Create a New Conda Environment**:
   - Run the following command to create a new environment named `geodepy_env`:
     ```sh
     conda create --name geodepy_env python=3.8
     ```
   - Activate the new environment:
     ```sh
     conda activate geodepy_env
     ```

3. **Install GDAL**:
   - Since GDAL is required, install it using conda-forge:
     ```sh
     conda install -c conda-forge gdal
     ```

4. **Install GeodePy and Dependencies**:
   - Clone the GeodePy repository from GitHub:
     ```sh
     git clone https://github.com/GeoscienceAustralia/GeodePy.git
     ```
   - Navigate to the GeodePy directory:
     ```sh
     cd GeodePy
     ```
   - Install the necessary dependencies:
     ```sh
     pip install -r requirements.txt
     ```
   - Install GeodePy:
     ```sh
     pip install .
     ```

### Running Jupyter Notebook and Applying GeodePy Functions

1. **Install Jupyter Notebook**:
   - In the Anaconda Prompt, while your environment is activated, run:
     ```sh
     conda install -c conda-forge notebook
     ```

2. **Start Jupyter Notebook**:
   - Run the following command to start Jupyter Notebook:
     ```sh
     jupyter notebook
     ```
   - A browser window will open. Navigate to the directory where you want to create your notebook and click "New" > "Python 3".

3. **Using GeodePy Functions in Jupyter Notebook**:

   - In your Jupyter Notebook, start by importing the necessary modules and loading your dataset. Here's a sample code snippet:

     ```python
     import pandas as pd
     from geodepy.height import GPS_to_AVWS, GPS_to_AHD, GPS_to_AUSGeoid09, GPS_to_AUSGeoid98

     # Load the file
     file_path = "TEST Lat Long Ellipsoidal.csv"
     data = pd.read_csv(las_file_path, header=None)
     data.columns = ["latitude", "longitude", "height"]

     # Extract GPS coordinates and heights
     latitudes = data.latitude
     longitudes = data.longitude
     gps_heights = data.height

     # Convert GPS heights using GeodePy functions
     avws_heights = [GPS_to_AVWS(lat, lon, h) for lat, lon, h in zip(latitudes, longitudes, gps_heights)]
     ahd_heights = [GPS_to_AHD(lat, lon, h) for lat, lon, h in zip(latitudes, longitudes, gps_heights)]
     ausgeoid09_heights = [GPS_to_AUSGeoid09(lat, lon, h) for lat, lon, h in zip(latitudes, longitudes, gps_heights)]
     ausgeoid98_heights = [GPS_to_AUSGeoid98(lat, lon, h) for lat, lon, h in zip(latitudes, longitudes, gps_heights)]

     # Create a DataFrame to display results
     df = pd.DataFrame({
         'Latitude': latitudes,
         'Longitude': longitudes,
         'GPS_Height': gps_heights,
         'AVWS_Height': avws_heights,
         'AHD_Height': ahd_heights,
         'AUSGeoid09_Height': ausgeoid09_heights,
         'AUSGeoid98_Height': ausgeoid98_heights
     })

     df.head()
     ```

4. **Save and Export Results**:
   - To save the DataFrame to a CSV file, you can use:
     ```python
     df.to_csv('converted_heights.csv', index=False)
     ```
