# Jakarta Urban Flood Susceptibility Assessment

An exploratory GeoAI workflow for mapping historical flood susceptibility in Jakarta, Indonesia. It combines Google Earth Engine (GEE) predictors and flood-event data with a scikit-learn Random Forest, then creates probability and susceptibility rasters.

> **Status:** Research/learning prototype. Results are not operational flood forecasts, engineering assessments, or validated risk maps.

## Project contents

| Path | Description |
| --- | --- |
| [src/jakarta_flood_susceptibility.py](src/jakarta_flood_susceptibility.py) | Full Google Colab workflow: GEE preparation, flood inventory, model evaluation, raster prediction, and mapping |
| [notebooks/jakarta_flood_susceptibility.py](notebooks/jakarta_flood_susceptibility.py) | Colab-exported Python workflow copy |
| [results/model_metrics.csv](results/model_metrics.csv) | Recorded training-sample and evaluation metrics |
| [results/feature_importance.csv](results/feature_importance.csv) | Random Forest impurity-based feature importance |
| [results/figures/](results/figures/) | Destination for generated result figures |
| [outputs/README.md](outputs/README.md) | Notes on generated output products |
| [docs/project_report.md](docs/project_report.md) | Project report outline |
| [requirements.txt](requirements.txt) | Python package dependencies |

## Workflow summary

The script uses the study-area rectangle `[106.75, -6.35, 107.00, -6.10]` and:

1. Creates a 2025 Sentinel-2 Surface Reflectance Harmonized composite and derives NDVI, NDWI, and NDBI.
2. Uses the SRTM global DEM to derive elevation and slope.
3. Finds Global Flood Database events intersecting the study area; in the recorded run, 19 events intersected and the 10 with the largest flooded-band sums were selected for a maximum-extent label composite.
4. Samples predictor and flood-label values and trains a class-weighted Random Forest.
5. Exports the predictor GeoTIFF through Google Drive and applies the model to create flood-probability and three-class susceptibility GeoTIFFs.
6. Produces a susceptibility map and saves the trained model.

The five model features, in raster band order, are `elevation`, `slope`, `NDVI`, `NDWI`, and `NDBI`.

## Run in Google Colab

The full script imports `google.colab`, authenticates with Earth Engine, exports through Google Drive, and uses `/content` paths. Run it in Google Colab rather than as a local desktop Python script.

1. Open the repository or upload [src/jakarta_flood_susceptibility.py](src/jakarta_flood_susceptibility.py) in a Colab session.
2. Ensure the dependencies in [requirements.txt](requirements.txt) are available in the runtime. Colab supplies the `google.colab` module.
3. Update `EE_PROJECT_ID` in the script to an Earth Engine-enabled Google Cloud project, then authenticate when prompted.
4. Review the study area, date range, sampling parameters, and thresholds before execution.
5. Run the script and inspect the exported files and evaluation output.

The script saves intermediate and final files under `/content` and exports the predictor raster to the `GeoAI_Jakarta` folder in Google Drive. The CSVs in `results/` record the metrics from the supplied run; they are not recomputed automatically by the script into the repository.

## Recorded model results

The saved evaluation used 2,899 samples: 2,793 non-flood (96.34%) and 106 flood (3.66%). It used a random stratified train/test split, not spatially independent validation.

| Metric | Result |
| --- | ---: |
| ROC-AUC | 0.9621 |
| Average precision (PR-AUC) | 0.6911 |
| Flood recall at probability threshold 0.50 | 0.52 |
| Flood recall at probability threshold 0.30 | 0.70 |

At the 0.30 threshold, flood recall increased while flood precision decreased to 0.56 (15 false positives and 8 false negatives in the held-out sample). For all recorded metrics, see [results/model_metrics.csv](results/model_metrics.csv).

### Feature importance

| Feature | Importance |
| --- | ---: |
| Elevation | 0.505765 |
| NDBI | 0.273999 |
| Slope | 0.083356 |
| NDWI | 0.071839 |
| NDVI | 0.065040 |

These are impurity-based scores from the fitted Random Forest; they indicate model reliance, not causal influence.

## Limitations and interpretation

- The predictors are based on a 2025 Sentinel-2 composite, while the flood labels combine historical events from different dates.
- The flood inventory and satellite observations have coverage, classification, and masking limitations that should be reviewed before interpretation.
- Random pixel-level splitting can place nearby, spatially correlated samples in both training and test sets, making the reported performance optimistic for geographic transfer.
- The flood class is rare in the sampled data; accuracy alone is not an adequate measure of performance.
- Susceptibility thresholds of 0.30 and 0.60 are exploratory, not calibrated engineering thresholds.
- The map represents susceptibility patterns only; it does not model flood depth, exposure, vulnerability, or real-time hazard.

Use spatially blocked and preferably temporally independent validation, verify source masks and event labels, and calibrate thresholds against independent observations before drawing operational conclusions.

## Data sources

- Sentinel-2 Surface Reflectance Harmonized (`COPERNICUS/S2_SR_HARMONIZED`)
- SRTM 1 Arc-Second Global (`USGS/SRTMGL1_003`)
- Global Flood Database (`GLOBAL_FLOOD_DB/MODIS_EVENTS/V1`)

Follow each provider's terms and cite the datasets appropriately when redistributing results.
