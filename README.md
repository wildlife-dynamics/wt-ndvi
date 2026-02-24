# NDVI Workflow

## Introduction

This workflow helps you to monitor vegetation health using satellite-derived NDVI (Normalized Difference Vegetation Index) data, allowing you to track greenness trends over time, compare current conditions against historical baselines, and identify areas of vegetation change across your regions of interest.

**What this workflow does:**
- Connects to **Google Earth Engine** to retrieve NDVI satellite imagery for your regions of interest
- Calculates NDVI values using **MODIS** satellite data products
- Compares current NDVI against historical baselines (min, max, and mean)
- Creates interactive trend charts showing how current vegetation compares to historical patterns
- Generates interactive maps with NDVI satellite overlays and ROI boundaries

**Who should use this:**
- Conservation managers monitoring vegetation health in protected areas
- Researchers analyzing land cover and vegetation change over time
- Anyone needing to visualize and track NDVI trends for specific geographic regions

## Prerequisites

Before using this workflow, you need:

1. **Ecoscope Desktop** installed on your computer
   - If you haven't installed it yet, please follow the installation instructions for Ecoscope Desktop

2. **Google Earth Engine Data Source** configured in Ecoscope Desktop
   - You must have already set up a connection to Google Earth Engine
   - Your data source should be configured with a valid GEE project ID and authentication credentials
   - You'll need to know the name of your configured data source (e.g., "ecoscope_poc")

3. **Region of Interest (ROI) data** available in one of these formats:
   - A local GeoPackage (`.gpkg`) or GeoParquet (`.parquet`) file on your computer
   - A remote URL pointing to a GeoPackage or GeoParquet file (e.g., Dropbox link)
   - A spatial features group configured in EarthRanger
   - Each ROI must have a name column to identify individual regions

## Installation

1. Open Ecoscope Desktop
2. Select "Workflow Templates"
3. Click "Add Workflow Template"
4. Copy and paste this URL https://github.com/wildlife-dynamics/wt-ndvi and wait for the workflow template to be downloaded and initialized
5. The template will now appear in your available template list

## Configuration Guide

Once you've added the workflow template, you'll need to configure it for your specific needs. The configuration form is organized into several sections.

### Basic Configuration

These are the essential settings you'll need to configure for every workflow run:

#### 1. Workflow Details
Give your workflow a name and description to help you identify it later.

- **Workflow Name** (required): A descriptive name for this workflow run and the dashboard title
  - Example: `"NDVI Workflow"`
- **Workflow Description** (optional): Additional details about this analysis
  - Example: `"Normalized Difference Vegetation Index Workflow"`

#### 2. Google Earth Engine Data Source
Select your Google Earth Engine connection.

- **Data Source** (required): Choose from your configured GEE data sources
  - Example: Select `ecoscope_poc` from the dropdown

#### 3. Time Range
Specify the time period for the NDVI analysis.

- **Since** (required): Use the calendar picker to select the start date and time
  - Example: `01/01/2023, 12:00 AM`
- **Until** (required): Use the calendar picker to select the end date and time
  - Example: `12/31/2023, 11:59 PM`
- **Timezone** (optional): Use the dropdown to select your timezone
  - Example: `Africa/Nairobi (UTC+03:00)`
  - Note: The workflow pulls all historical MODIS images from Google Earth Engine to calculate baseline statistics (min, max, mean) for comparison. The trend chart displays only the current period you select, overlaid with the historical baseline. The NDVI map shows the mean NDVI for the selected time range only.

#### 4. Group Data (Optional)
Organize your data into separate dashboard views based on ROI name.

- **Group by**: Create separate outputs grouped by:
  - Category: ROI Name - creates separate views for each region of interest
  - Default and highly recommended: Groups by ROI Name

#### 5. Load ROI
Specify where your Region of Interest boundaries come from. Choose one of three options:

**Option A: Local File**
- **File Path** (required): Path to a GeoPackage (`.gpkg`) or GeoParquet (`.parquet`) file on your computer
  - Example: `"/Users/yourname/Downloads/AOIs.gpkg"`
- **Name Column** (required): Column in the file to use as region name
  - Default: `"name"`
  - Example: `"region"`
- **Layer** (optional, advanced): Layer name within a GeoPackage file if it contains multiple layers

**Option B: Remote URL**
- **URL** (required): URL to a GeoPackage or GeoParquet file hosted online
  - Example: `"https://www.dropbox.com/s/nvdmidz1o2duyl3/AOIs.gpkg?dl=1"`
- **Name Column** (required): Column to use as region name
  - Default: `"name"`
- **Layer** (optional, advanced): Layer name for multi-layer GeoPackage files

**Option C: EarthRanger**
- **Data Source** (required): Select one of your configured EarthRanger data sources
  - Example: `"mep_dev"`
- **Spatial Features Group Name** (required): Name of the spatial features group in EarthRanger
  - Example: `"SpatialGrouperTest"`

#### 6. NDVI Method
Choose which MODIS satellite data product to use for NDVI calculation.

- **NDVI Method** (optional): Select the satellite data source
  - Default: `"MODIS MYD13A1 16-Day Composite"`
  - Options:
    - **MODIS MYD13A1 16-Day Composite**: Uses pre-calculated NDVI from 16-day composites. Provides quality-filtered "best pixel" values at 500m resolution with ~0.025 accuracy. Better for phenology studies but may saturate in dense canopies.
    - **MODIS MCD43A4 Daily NBAR**: Uses daily nadir BRDF-adjusted reflectance. Computes NDVI from NIR/Red bands with view-angle correction for consistent measurements. Higher temporal resolution but more susceptible to cloud gaps.

#### 7. NDVI Trend
Configure how historical NDVI data is grouped for comparison.

- **Grouping Unit** (optional): Temporal unit for grouping historical data when calculating statistics
  - Default: `"month"`
  - Options:
    - `month`: Compare against the same calendar month (1-12)
    - `week`: Compare against the same ISO week number (1-53)
    - `day_of_year`: Compare against the same day of year (1-366)
    - `modis_16_day`: Compare against the same MODIS 16-day composite period (0-22). Only applicable to "MODIS MYD13A1 16-Day Composite" NDVI method.

### Advanced Configuration

These optional settings provide additional control over your workflow:

#### Map Base Layers
Customize the base map layer displayed beneath the NDVI overlay.

- **Map Base Layers** (optional): Select tile layers to use as base layers in map outputs. The first layer in the list will be the bottommost layer displayed.
  - Default: Terrain (World Topo Map)
  - Available presets: Open Street Map, Roadmap, Satellite, Terrain, LandDx, USGS Hillshade
  - Custom Layer (Advanced): Provide your own tile URL, opacity, and zoom level settings
  - **Layer Opacity**: Set layer transparency from 1 (fully visible) to 0 (hidden)

## Running the Workflow

Once you've configured all the settings:

1. **Review your configuration**
   - Double-check your time range, GEE data source, and ROI settings

2. **Save and run**
   - Click the "Submit" and the workflow will show up in "My Workflows" table button in Ecoscope Desktop
   - Click on "Run" and the workflow will begin processing

3. **Monitor progress and wait for completion**
   - You'll see status updates as the workflow runs
   - Processing time depends on:
     - The size of your date range
     - Number of regions of interest
     - The NDVI method selected (daily NBAR may take longer than 16-day composites)
     - Google Earth Engine server load
   - The workflow completes with status "Success" or "Failed"

## Understanding Your Results

After the workflow completes successfully, you'll find your outputs in the designated output folder.

### Visual Outputs (Dashboard)

The workflow creates an interactive dashboard with two main visualizations:

#### NDVI Trends Chart
- **Format**: Interactive line chart with historical comparison bands
- **Features**:
  - X-axis: Date (within your selected time range)
  - Y-axis: NDVI value (ranging from -1 to 1, where higher values indicate more vegetation)
  - **Current NDVI line**: Shows actual NDVI values for your selected time period
  - **Historic Mean line**: Shows the average NDVI for the same temporal grouping unit across all available years
  - **Historic Min-Max band**: Shaded region showing the historical range (minimum to maximum) of NDVI values
  - Interactive hover: Shows exact values when you mouse over data points
- **How to interpret**:
  - NDVI values above the historic mean suggest above-average vegetation greenness
  - NDVI values below the historic mean may indicate drought stress, land use change, or seasonal effects
  - Values outside the historic min-max band are unusual and warrant investigation

#### NDVI Map
- **Format**: Interactive map with satellite NDVI overlay
- **Features**:
  - Base map layer (Terrain by default)
  - NDVI satellite imagery overlay showing vegetation index values as colors
  - ROI boundary outlines in green
  - Zoom and pan controls
  - Shows the mean NDVI for the selected time range
- **How to interpret**:
  - Greener colors indicate higher NDVI (healthier, denser vegetation)
  - Brown/yellow colors indicate lower NDVI (sparse vegetation, bare soil, or water)
  - Use the ROI boundaries to compare vegetation conditions across regions

### Grouped Outputs

If you configured data grouping (by ROI Name):
- Dashboard visualizations will have multiple views, with each region selectable from the dashboard
- Each view shows the NDVI trend chart and map for that specific region

## Common Use Cases & Examples

Here are some typical scenarios and how to configure the workflow for each:

### Example 1: Annual NDVI Overview for All Regions
**Goal**: Monitor vegetation trends across all regions of interest for an entire year

**Configuration**:
- **Time Range**:
  - Since: `2023-01-01T00:00:00`
  - Until: `2023-12-31T23:59:59`
- **GEE Data Source**: `"ecoscope_poc"`
- **ROI**: Remote URL - `"https://www.dropbox.com/s/nvdmidz1o2duyl3/AOIs.gpkg?dl=1"`
- **NDVI Method**: `"MODIS MYD13A1 16-Day Composite"`
- **Grouping Unit**: `month`
- **Group by**: ROI Name

**Result**:
- Separate NDVI trend charts for each region, comparing current monthly values against historical monthly baselines
- NDVI map showing mean vegetation index for the year with ROI boundaries

---

### Example 2: Higher Temporal Resolution with Daily NBAR
**Goal**: Get more frequent NDVI measurements using calculated NDVI from daily reflectance data

**Configuration**:
- **Time Range**:
  - Since: `2023-01-01T00:00:00`
  - Until: `2023-12-31T23:59:59`
- **GEE Data Source**: `"ecoscope_poc"`
- **ROI**: Remote URL - `"https://www.dropbox.com/s/nvdmidz1o2duyl3/AOIs.gpkg?dl=1"`
- **NDVI Method**: `"MODIS MCD43A4 Daily NBAR"`
- **Grouping Unit**: `week`
- **Group by**: ROI Name

**Result**:
- More granular NDVI trend charts with weekly historical comparison
- Higher temporal detail but potentially more cloud-related gaps in the data

---

### Example 3: Using a Local ROI File
**Goal**: Analyze NDVI for regions defined in a local GeoPackage file

**Configuration**:
- **Time Range**:
  - Since: `2023-01-01T00:00:00`
  - Until: `2023-12-31T23:59:59`
- **GEE Data Source**: `"ecoscope_poc"`
- **ROI**: Local File
  - File Path: `"/Users/yourname/Downloads/AOIs.gpkg"`
  - Name Column: `"region"`
- **NDVI Method**: `"MODIS MYD13A1 16-Day Composite"`
- **Grouping Unit**: `month`

**Result**:
- NDVI analysis using your custom region boundaries
- Regions grouped by the values in the "region" column of your file

---

### Example 4: Using EarthRanger Spatial Features
**Goal**: Analyze NDVI for regions defined as spatial features in EarthRanger

**Configuration**:
- **Time Range**:
  - Since: `2023-01-01T00:00:00`
  - Until: `2023-12-31T23:59:59`
- **GEE Data Source**: `"ecoscope_poc"`
- **ROI**: EarthRanger
  - Data Source: `"mep_dev"`
  - Spatial Features Group Name: `"SpatialGrouperTest"`
- **NDVI Method**: `"MODIS MYD13A1 16-Day Composite"`
- **Grouping Unit**: `month`

**Result**:
- NDVI analysis using region boundaries from your EarthRanger system
- Useful when your ROI boundaries are already managed in EarthRanger

## Troubleshooting

### Common Issues and Solutions

#### Workflow fails to start
**Problem**: The workflow won't run or immediately fails

**Solutions**:
- Verify your Google Earth Engine data source is properly configured with a valid project ID
- Check that you have network connectivity to Google Earth Engine servers
- Ensure your GEE authentication credentials haven't expired
- Confirm the data source name matches exactly

#### No NDVI data returned
**Problem**: Workflow completes but produces empty charts or maps

**Solutions**:
- Verify the date range is correct (start date should be before end date)
- Check that your ROI boundaries are valid geographic polygons
- Ensure your ROI file is in a supported format (GeoPackage or GeoParquet)
- Verify the name column exists in your ROI file
- Try a broader date range to confirm data availability

#### ROI file cannot be loaded
**Problem**: Error when loading your region of interest data

**Solutions**:
- For local files: Verify the file path is correct and the file exists on your computer
- For remote URLs: Ensure the URL is publicly accessible (for Dropbox, use `?dl=1` at the end)
- Verify the file is a valid GeoPackage (`.gpkg`) or GeoParquet (`.parquet`) file
- Check that the name column specified exists in your file (default is `"name"`)

#### Workflow runs very slowly
**Problem**: The workflow takes an extremely long time to complete

**Solutions**:
- Reduce the date range to smaller time periods
- Use the "MODIS MYD13A1 16-Day Composite" method instead of "Daily NBAR" (fewer images to process)
- Reduce the number or size of your regions of interest
- Google Earth Engine may have server-side delays; try again later
- The first run may take longer as the environment gets warmed up. The following ones should be faster.

#### Authentication errors with Google Earth Engine
**Problem**: Errors related to GEE login or permissions

**Solutions**:
- Re-configure your Google Earth Engine data source in Ecoscope Desktop
- Verify your GEE project ID is correct
- Re-authenticate with `earthengine authenticate` if your credentials have expired
- Ensure your Google account has access to Google Earth Engine

#### NDVI map shows no satellite overlay
**Problem**: Map displays ROI boundaries but no NDVI color overlay

**Solutions**:
- Verify your time range contains dates with available MODIS imagery
- Check that your regions of interest are within MODIS coverage areas
- Try a different NDVI method to rule out data gaps
- Ensure your GEE data source has proper permissions to access MODIS collections

#### Unexpected NDVI values
**Problem**: NDVI values seem too high, too low, or inconsistent

**Solutions**:
- NDVI values range from -1 to 1; values near 0 may indicate bare soil, water, or clouds
- Dense vegetation typically shows NDVI between 0.3 and 0.8
- The "16-Day Composite" method filters for quality, while "Daily NBAR" may include more cloud-affected pixels
- Verify your ROI boundaries don't include large water bodies, which have negative NDVI values
- Consider using the `month` grouping unit for smoother historical comparisons
