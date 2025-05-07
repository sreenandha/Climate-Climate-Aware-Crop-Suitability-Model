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

  
## Methodology

The Climate-Aware Crop Suitability Model follows a structured, multi-phase pipeline:

### 1. **Data Acquisition and Integration**
- Extracted detailed soil attributes from SSURGO using Microsoft Access and ArcGIS Pro.
- Retrieved historical daily weather data (2021–2024) via NASA POWER API.
- Retrieved seasonal forecast data (2025) from ECMWF SEAS5.
- Merged all datasets by latitude and longitude, ensuring alignment of soil and climate data for each location.

### 2. **Feature Engineering**
- Created derived climate indicators such as:
  - Growing Degree Days (GDD)
  - Heat and cold stress days
  - Temperature and rainfall trends
- Constructed a **Climate Pressure Index (CPI)** to quantify environmental stress at each location.
- Engineered soil features like:
  - Soil quality index
  - Depth ratio
  - Water efficiency score

### 3. **Model Training**
- Used a **Random Forest Regressor** to predict crop yield (`nirr_yield_ton_acre`) based on:
  - Soil features
  - Historical climate trends
  - Encoded crop types (via `crop_label`)
- Trained the model on 2021–2024 data covering 8 different crops.

### 4. **Forecasting and Suitability Classification**
- Applied the trained model to 2025 climate forecasts to generate predicted yields for each crop at each location.
- Compared predicted yield to historical yield to compute yield change.
- Flagged crops with significant yield decline as “Yield Drop” and stable/improved crops as “Stable or Improved”.

## Model Training and Forecasting

### Multi-Crop Yield Prediction

A Random Forest Regressor was trained to estimate yield (`nirr_yield_ton_acre`) for 8 crop types using soil and climate features. The model was designed to be crop-aware by including a `crop_label` feature that numerically represents each crop.

#### Input Features
- **Soil**: sand/clay %, organic matter, pH, water storage, drainage class (one-hot encoded)
- **Climate**: temperature trends, rainfall trends, GDD, heat stress index, humidity stress
- **Engineered Indices**: Climate Pressure Index, soil quality score, depth ratios

#### Target Variable
- Yield in tons/acre (`nirr_yield_ton_acre`)

### Forecasting Yield for 2025

- Forecast data from ECMWF SEAS5 was processed to create the same trend features as the historical set.
- The trained model was applied to each location-crop combination for 2025 to estimate future yield.
- A comparison with 2021–2024 actual yield was used to detect yield shifts under climate pressure.

### Suitability Flagging

Based on the difference between actual and predicted yield:
- `Yield Drop` was assigned if yield decreased significantly (>0.3 tons/acre)
- `Stable or Improved` if yield remained the same or improved

## Results and Interpretation

The model was used to predict 2025 crop yields under changing climate conditions and compare them with actual yields from 2021–2024. A yield difference threshold of ±2 tons/acre was applied to filter out unstable or unrealistic crop responses.

### Key Insights

- **Location-specific filtering** identified both stable and risky crops based on predicted yield shifts.
- Crops with **minimal yield differences** (±2 tons/acre) were grouped by location and flagged:
  - **Good Crops**: Those maintaining or improving yields under forecast conditions
  - **Bad Crops**: Those showing noticeable yield drops even if within ±2 range

### Stable Crop Suitability Summary

| Latitude   | Longitude  | Crop Name | Avg Yield Difference | Flag         |
|------------|------------|-----------|-----------------------|--------------|
| 38.128498  | -75.702402 | Soybeans  | 0.250                 | Good Crop |
| 38.128498  | -75.702402 | Wheat     | -0.158                | Bad Crop  |
| 38.128498  | -75.702402 | Corn      | -1.791                | Bad Crop  |
| 38.158015  | -75.681655 | Soybeans  | 0.266                 | Good Crop |
| 38.158015  | -75.681655 | Wheat     | -0.142                | Bad Crop  |

### Interpretation

- **Soybeans** consistently performed well across both stable locations, making it a top candidate for climate-resilient cropping.
- **Corn**, although historically productive, shows significant yield drops and may be less suitable in southern counties under 2025 forecasts.
- **Wheat** demonstrated mild yield reduction but still falls within manageable thresholds, depending on farmer risk tolerance.

The flagged table above is helpful for localized agricultural planning, highlighting which crops can remain viable and which might require reconsideration due to climate pressures.

## How to Use

This project is designed to guide climate-resilient crop selection at the local scale (latitude/longitude). Follow the steps below to replicate the analysis:

1. **Prepare Input Data**
   - Use USDA SSURGO soil datasets and compute features such as texture, organic matter, drainage class, pH, and water retention.
   - Acquire historical (2021–2024) weather data from NASA POWER and forecast (2025) from ECMWF SEAS5.
   - Align all data by `latitude` and `longitude`.

2. **Build and Train Yield Model**
   - Train a multi-crop Random Forest Regressor using historical climate and soil data.
   - Encode `crop_label` to support prediction across multiple crop types.

3. **Forecast Yield for 2025**
   - Preprocess 2025 climate forecasts into trend-based features (temperature, rainfall, stress indices).
   - Apply the trained model to generate predicted yields per crop per location.

4. **Suitability Evaluation**
   - Compare predicted vs actual yields.
   - Flag crops with stable or improved performance (e.g., yield drop ≤ 2 tons/acre).
   - Visualize results using maps and location-wise tables for farmer guidance.

5. **Visual and Tabular Output**
   - Generate bar charts and maps showing the best and worst crops for each region.
   - Use filtered tables to identify which crops to recommend or discourage at the location level.

## Future Enhancements

Several advanced capabilities are planned to make this system more dynamic, adaptive, and farmer-centered:

### Geospatial Generalization
- Extend from individual point-based predictions (lat/lon) to county- or ZIP-level aggregation for easier regional analysis.

### Deep Learning Yield Models
- Replace traditional Random Forests with Multi-Layer Perceptrons (MLPs) or Graph Neural Networks (GNNs) to model complex interactions between soil-climate-crop systems.

### Reinforcement Learning for Adaptive Planning
- Introduce **Reinforcement Learning (RL)** to simulate and optimize multi-season farming strategies.
  - The agent (farmer) receives rewards based on yield stability, climate resilience, and resource efficiency.
  - Actions include crop selection, irrigation timing, or rotation decisions.
  - The model learns policies that adapt to uncertain weather, changing soil health, and market constraints.

### Crop Rotation and Resource Optimization
- Expand from single-season prediction to multi-season planning, optimizing for soil health and long-term productivity.

### Real-Time Forecast Integration
- Connect to live APIs (e.g., OpenWeather, ECMWF) to update yield forecasts and crop recommendations dynamically during the growing season.

### Interactive Farmer Dashboard
- Build a user-friendly web application (Streamlit or Dash) where users can:
  - Select their location
  - View crop suitability scores
  - Download yield predictions
  - Upload actual performance data

### Satellite Monitoring (NDVI)
- Integrate NDVI-based vegetation indices from Sentinel or Landsat satellites to validate predicted performance and detect early crop stress.

### Farmer Feedback Loop
- Collect actual yield outcomes from farmers to fine-tune predictions and personalize recommendations over time.

## Contributors
- **Sree Nandha Sivakumaran**  
- **Akshay Jeyakumar**  
- **Sri Harsha Reddy Adapala**  
