# Climate-Aware Crop Suitability Model 

## Project Overview

The Climate-Aware Crop Suitability Model (CCACS) is a data-driven AI tool that helps evaluate the impact of climate change on crop yield potential across different soil types and weather patterns. It predicts the most suitable crops for a given location and season by integrating historical and forecasted climate data with detailed soil profiles.

This system empowers farmers, agricultural planners, and policymakers to make smarter, climate-resilient decisions for crop planning and sustainability.

## Table of Contents

1. [Introduction](#introduction)
2. [Key Features](#key-features)
3. [Data Sources](#data-sources)
4. [Methodology](#methodology)
5. [Model Training and Forecasting](#model-training-and-forecasting)
6. [Results and Interpretation](#results-and-interpretation)
7. [How to Use](#how-to-use)
8. [Future Enhancements](#future-enhancements)
9. [Contributors](#contributors)

## Introduction

Climate change is rapidly transforming the agricultural landscape, altering growing seasons, rainfall patterns, and temperature extremes. Traditional farming practices—often based on historical intuition—struggle to adapt to these shifts.

This project aims to bridge that gap by using data science to forecast crop performance under evolving climate conditions. By combining long-term weather trends with detailed soil characteristics, the model offers evidence-based recommendations to support sustainable farming decisions across Maryland.


## Key Features

### Climate Trend Analysis
- Computes multi-year weather trends using NASA POWER (2021–2024) and ECMWF SEAS5 forecasts (2025).
- Derives features such as temperature trend, precipitation change, GDD, heat/cold stress, and a composite Climate Pressure Index (CPI).

### Soil Feature Integration
- Extracts detailed soil attributes from SSURGO including:
  - Sand/Clay composition
  - Organic matter
  - Soil pH
  - Available water capacity
  - Drainage class
- Builds engineered indices like Soil Quality Index and Water Efficiency.

### Multi-Crop Yield Prediction
- Trains a Random Forest Regressor using soil and historical climate data to predict yield (`nirr_yield_ton_acre`) for 8 crop types.
- Includes `crop_label` to support crop-specific yield modeling.

### Forecast-Based Suitability Assessment
- Uses 2025 forecasted climate trends to predict future yield.
- Compares with historical yield to assess climate-driven suitability shifts.
- Flags crops at risk due to expected yield decline.

### Location-Specific Analysis
- All results are tied to latitude and longitude for spatial mapping.
- County-level insights are linked through reverse geocoding and soil datasets.

### Yield Comparison Dashboard (Tabular)
- Displays previous vs predicted yield, crop name, location, and a suitability flag.
- Highlights yield drop risk using simple thresholds.
  
## Data Sources

This project combines multiple trusted, scientific datasets to build an accurate crop suitability model:

- **NASA POWER (2021–2024)**  
  Provides daily weather data including temperature, precipitation, radiation, wind speed, and humidity. Used to compute climate trend indicators and stress indices.

- **ECMWF SEAS5 Seasonal Forecast (2025)**  
  Delivers forecasted weather data for the 2025 growing season. Enables future yield prediction and suitability analysis under projected climate conditions.

- **SSURGO Soil Database (USDA NRCS)**  
  Contains high-resolution soil data across U.S. counties. Key features include sand/clay percentages, organic matter, drainage class, pH, and available water capacity.

- **County Shapefiles & Lat/Lon Grids**  
  Used for spatial merging of soil and climate data at the sub-county level across 16 Maryland counties.
