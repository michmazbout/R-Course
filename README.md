# Spatial and Temporal Analysis of Temperature Sensor Data

![GitHub](https://img.shields.io/badge/version-1.0-blue) 
![R](https://img.shields.io/badge/language-R-%2765DEFF) 
![License](https://img.shields.io/badge/license-MIT-green)

This repository contains R scripts for analyzing temperature sensor data, focusing on **distribution validation**, **geospatial interpolation**, and **environmental covariate effects**. The project is divided into two core analyses:

1. **Sensor-Level Temperature Distributions**: Exploratory Data Analysis (EDA) and normality assessments for logger data.  
2. **Interpolated Temperature Modeling**: Geospatial regression and monthly variability analysis.

---

## 📂 Files

### Core Scripts
| File | Description |
|------|-------------|
| [**Sensor-Data-EDA.R**](Sensor-Data-EDA.R) | EDA, visualizations (histograms, QQ-plots), and Lilliefors normality tests for sensor-level data. |  
| [**Geospatial-Temperature-Models.R**](Geospatial-Temperature-Models.R) | Interpolated temperature analysis, regression (altitude/slope/aspect effects), and monthly correlation tests. |

### Supporting Data
- `DATA_LOGGER_Nant.RData`: Raw temperature logger dataset (sensor IDs, timestamps, measurements).  
- `Temperatures_Interpolees.RData`: Interpolated temperature data with geospatial covariates (elevation, slope, aspect).  

*(Note: Data files are not included in this repo due to size constraints. Contact the author for access.)*

---

## 🔍 Key Analyses

### 1. Sensor Data EDA
- **Visualizations**: Histograms, boxplots, and faceted density plots by sensor ID.  
- **Normality Tests**: Lilliefors (K-S) tests and square-root transformations for non-normal distributions.  
- **Categorical Tests**: Chi-squared uniformity checks for logger metadata (orientation, land cover).  

### 2. Geospatial Modeling
- **Sampling**: Density-weighted sampling to ensure representativity (1300–2000m elevation range).  
- **Regression**: Linear models (`interp ~ Z + Slope + aspect`) with monthly subgroup analyses.  
- **Correlations**: Pearson tests for temperature vs. altitude, slope, and aspect (grouped by month).  

---

## 📊 Example Outputs

### Sensor-Level EDA
| Histograms by Sensor ID | QQ-Plots (Normalized) |
|-------------------------|-----------------------|
| ![Histograms](images/sensor_histograms.png) | ![QQ-Plots](images/qq_plots.png) |

### Geospatial Results
| Monthly Correlation Trends | Residual Diagnostics |
|---------------------------|----------------------|
| ![Monthly](images/monthly_correlations.png) | ![Residuals](images/residual_plots.png) |

*(Replace with actual output paths or embed dynamic HTML reports.)*

---

## 🛠️ Setup

### Dependencies
```r
install.packages(c(
  "ggplot2", "dplyr", "lubridate", "nortest", 
  "gridExtra", "factoextra", "purrr", "knitr"
))
