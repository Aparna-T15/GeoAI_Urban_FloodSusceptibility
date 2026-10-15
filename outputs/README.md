# Jakarta Urban Flood Susceptibility Assessment Using GeoAI

An exploratory geospatial machine-learning workflow for urban flood susceptibility assessment in Jakarta, Indonesia, integrating Sentinel-2 spectral indices, terrain variables, historical flood information, and Random Forest classification.

## Overview

Urban flooding is a major challenge across Southeast Asia. This project demonstrates how remote sensing, GIS, and machine learning can be combined to investigate spatial patterns associated with historical flooding.

The workflow uses Google Earth Engine for satellite-data processing and flood inventory preparation, followed by Python-based model training, evaluation, raster prediction, and visualisation.

**Project status:** Exploratory research prototype.

## Objectives

* Integrate satellite-derived spectral indices and terrain variables.
* Construct a historical flood-label composite using the Global Flood Database.
* Train and evaluate a Random Forest classifier.
* Investigate the relative importance of environmental predictors.
* Generate georeferenced flood-class probability and susceptibility rasters.
* Document limitations and potential extensions for urban flood-resilience research.

## Technologies

* Python
* Google Earth Engine
* Google Colab
* Sentinel-2 Surface Reflectance
* SRTM Digital Elevation Model
* geemap
* NumPy and Pandas
* Rasterio
* Scikit-learn
* Matplotlib

## Data Sources

| Dataset                                   | Purpose                                            |
| ----------------------------------------- | -------------------------------------------------- |
| Sentinel-2 Surface Reflectance Harmonized | Spectral indices and land-cover-related predictors |
| USGS SRTM Global 1 Arc-Second             | Elevation and slope                                |
| Global Flood Database                     | Historical flood labels                            |

The Sentinel-2 composite covers 2025. The flood inventory was constructed from selected historical events intersecting the study area.

## Methodology

### 1. Predictor preparation

Five predictors were generated:

* Elevation
* Slope
* Normalized Difference Vegetation Index (NDVI)
* Normalized Difference Water Index (NDWI)
* Normalized Difference Built-up Index (NDBI)

### 2. Flood inventory

Historical flood events intersecting the study area were identified using the Global Flood Database. Ten events were selected based on the summed values of their flooded bands and combined into a maximum-value composite.

### 3. Model training

A class-weighted Random Forest classifier was trained using 2,899 sampled records.

The sampled dataset contained 106 flood-labelled samples and 2,793 non-flood-labelled samples.

### 4. Evaluation

Performance was evaluated using confusion matrices, precision, recall, F1-score, ROC-AUC, and average precision.

### 5. Spatial prediction

The trained model was applied to a multiband predictor GeoTIFF to generate flood-class probability and susceptibility maps.

## Preliminary Results

The model was evaluated using a randomly held-out test subset.

| Metric                               | Result |
| ------------------------------------ | -----: |
| ROC-AUC                              | 0.9621 |
| Average precision                    | 0.6911 |
| Flood-class recall at threshold 0.50 |   0.52 |
| Flood-class recall at threshold 0.30 |   0.70 |

At a threshold of 0.30, the classifier identified more flood-labelled test samples, but also produced more false positives.

### Feature Importance

| Predictor | Importance |
| --------- | ---------: |
| Elevation |     0.5058 |
| NDBI      |     0.2740 |
| Slope     |     0.0834 |
| NDWI      |     0.0718 |
| NDVI      |     0.0650 |

Elevation and NDBI had the highest impurity-based feature-importance scores in the fitted model.

These values describe the fitted model and should not be interpreted as causal effects.

## Outputs

The workflow generates:

* A predictor GeoTIFF
* A flood-class probability GeoTIFF
* A three-class susceptibility GeoTIFF
* A PNG susceptibility map
* A serialized Random Forest model

Large generated rasters and model files are excluded from the repository by default.

## Limitations

* The predictors represent a 2025 Sentinel-2 composite, whereas the flood inventory combines historical events from different dates.
* The flood inventory may contain observation and classification uncertainties.
* Flood-labelled samples constitute only 3.66% of the sampled dataset.
* Random pixel-level train-test splitting may overestimate performance because of spatial dependence.
* The classifier's probability estimates have not been calibrated against independent flood observations.
* The susceptibility classes use exploratory thresholds rather than validated engineering-risk categories.
* The current implementation assesses flood susceptibility, not comprehensive flood risk or resilience.

The reported metrics are preliminary and should not be interpreted as evidence of operational predictive performance.

## Future Work

* Develop event-specific flood inventories and temporally matched predictors.
* Incorporate rainfall, drainage networks, impervious-surface information, and other relevant variables.
* Implement spatially blocked validation.
* Compare alternative machine-learning models.
* Investigate explainable GeoAI techniques such as permutation importance and SHAP.
* Integrate street-view imagery and socio-demographic data.
* Evaluate model transferability across Southeast Asian cities.

## Reproducibility

The workflow is designed for Google Colab and requires access to Google Earth Engine.

1. Open the notebook in Google Colab.
2. Configure an eligible Google Earth Engine Cloud project.
3. Authenticate with Earth Engine.
4. Review the dataset definitions, study area, and export settings.
5. Execute the workflow and inspect the generated outputs.

See `requirements.txt` for the initial Python dependency list.

## Disclaimer

This project is an exploratory research prototype for educational and research purposes. Its outputs are not validated flood forecasts, engineering assessments, or operational disaster-management products.

## Author

Developed as a portfolio project in remote sensing, GIS, and GeoAI, with an emphasis on urban flood susceptibility and climate resilience research.
