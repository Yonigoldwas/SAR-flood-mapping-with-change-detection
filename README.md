# Sentinel-1 SAR Flood Mapping  
### Change Detection Workflow for Flood Extent, Exposure, and Impact Assessment

This repository contains a complete Google Earth Engine workflow for flood detection and impact assessment using Sentinel-1 SAR Ground Range Detected (GRD) data. The script follows a change-detection approach using pre- and post-flood SAR mosaics, applies flood refinement masks, and estimates both inundated area and exposed assets.

The workflow includes flood mapping, population exposure, agricultural land impact, urban area impact, visualization, and export tools.

---

## Overview

This flood-mapping workflow performs the following:

* Loads Sentinel-1 SAR GRD imagery before and after a flood event  
* Applies speckle reduction through focal filtering  
* Computes change detection `(after / before)` to identify potential flooding  
* Masks out permanent water, steep slopes, and noisy isolated pixels  
* Calculates flood extent area in hectares  
* Computes exposed population using GHSL population data  
* Computes affected croplands using both CDL and MODIS Land Cover  
* Computes affected urban area  
* Displays multi-layer visualization including population exposure  
* Exports flood rasters and polygons  

The script also generates a complete user interface panel summarizing flood extent and exposure statistics.

---

## Data Sources

### Sentinel-1 SAR (GRD)
**Collection**: `COPERNICUS/S1_GRD`  
* Mode: IW  
* Polarization: VH (default, recommended for water sensitivity)  
* Includes thermal noise removal, radiometric calibration, terrain correction  

### JRC Global Surface Water
**Collection**: `JRC/GSW1_0/GlobalSurfaceWater`  
* Used to mask permanent water (seasonality ≥ 10)

### HydroSHEDS DEM
**Collection**: `WWF/HydroSHEDS/03VFDEM`  
* Used to remove flooding on slopes > 5 percent

### GHSL Population Density
**Collection**: `JRC/GHSL/P2016/POP_GPW_GLOBE_V1/2015`  
* Used to estimate population exposed to flooding

### USDA Cropland Data Layer (CDL)
**Collection**: `USDA/NASS/CDL`  
* Used to compute cultivated area affected by flooding

### MODIS Land Cover (MCD12Q1)
**Collection**: `MODIS/006/MCD12Q1`  
* Used to identify croplands and urban areas at 500 m resolution

---

## Workflow Summary

### 1. Define Flood Periods
Set date ranges for pre-flood and post-flood SAR collections.

### 2. Sentinel-1 Data Filtering
Images are filtered by  
* Instrument mode  
* Polarization (VH or VV)  
* Orbit direction (ascending or descending)  
* Spatial extent  
* Resolution (10 m)

### 3. Preprocessing
* Mosaic pre- and post-flood images  
* Clip to AOI  
* Apply focal mean smoothing for speckle suppression  

### 4. Flood Detection
Steps include

* Compute ratio image: `after / before`  
* Apply threshold (`difference_threshold`)  
* Mask permanent water using JRC seasonality  
* Apply connectivity filter to remove noise  
* Mask slopes above 5 percent  

### 5. Area Estimation
Compute flood area by

* Multiplying flood mask by pixel area  
* Summing flooded area in m²  
* Converting results to hectares  

### 6. Exposure Analysis

#### Population Exposure  
* Reproject flood mask to GHSL resolution  
* Mask population density layer  
* Sum exposed population

#### Affected Cropland  
* CDL cultivated mask (2 = cultivated)  
* MODIS LC cropland classes (12 and 14)  
* Reproject flood layer to MODIS scale  
* Calculate cropland area affected by flooding (ha)

#### Affected Urban Area  
* MODIS LC class 13  
* Flood-masked urban pixels  
* Compute impacted area (ha)

### 7. Visualization
Layers displayed include

* SAR before flood  
* SAR after flood  
* Difference layer  
* Flood extent  
* Population density  
* Exposed population  
* Cropland and affected cropland  
* Urban and affected urban  
* MODIS land cover  

Includes interactive UI panels and legends.

### 8. Exports
The script exports:

* Flood raster (GeoTIFF)  
* Flood polygons (SHP)  
* Exposed population raster (GeoTIFF)  

---

## Key Parameters

| Parameter | Description |
|----------|-------------|
| `polarization` | VH (default) or VV |
| `pass_direction` | ASCENDING or DESCENDING |
| `difference_threshold` | Change detection threshold |
| `smoothing_radius` | Speckle filtering radius in meters |
| `before_start / before_end` | Pre-flood dates |
| `after_start / after_end` | Post-flood dates |

---

## Outputs

The script prints or displays

* Flood extent area in hectares  
* Exposed population count  
* CDL-based cultivated area affected  
* MODIS cropland affected  
* Urban area affected  
* Complete map visualization  
* Flood polygons and rasters for download  

---

## Usage

1. Open the script in the Google Earth Engine Code Editor.  
2. Draw and name the geometry as `geometry`.  
3. Modify flood dates and SAR settings if needed.  
4. Run the script.  
5. Review the printed statistics and map layers.  
6. Export products from the Tasks tab.

---

## Notes

* Sentinel-1 SAR is unaffected by clouds, making it highly suitable for flood monitoring.  
* Thresholds may need manual tuning depending on study area, season, and backscatter characteristics.  
* Exposure and land-use results depend on the resolution of GHSL (250 m) and MODIS (500 m).  
* Results are intended for rapid assessment and not official mapping.  

---

## Attribution

Original structure based on UN-SPIDER flood mapping methods (2019).  
Expanded and adapted for a full end-to-end workflow.

