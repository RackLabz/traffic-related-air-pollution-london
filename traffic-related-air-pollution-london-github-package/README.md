# Traffic-Related Air Pollution Exposure in London

## Integrating Air Quality, Traffic, Spatial and Temporal Data for Statistical Analysis and Machine Learning

### Project Overview

This project investigates how traffic-related exposure, road proximity, spatial characteristics and temporal patterns are associated with PM2.5 and NO₂ concentrations in London.

The project combines high-frequency air-quality observations from the Breathe London AQMesh network with London Atmospheric Emissions Inventory (LAEI) traffic data and monitoring-site metadata.

The analysis is designed as an observational data science study. Statistical analysis is used to examine associations, while machine learning is used to evaluate predictive performance. The results are not interpreted as evidence of causality.

---

## Research Question

> How are traffic intensity and proximity to roads associated with urban PM2.5 and NO₂ concentrations, and can traffic, spatial and temporal characteristics be used to predict pollutant concentrations?

### Research Questions

1. Is traffic intensity associated with NO₂ concentration?
2. Is traffic intensity associated with PM2.5 concentration?
3. Is proximity to roads associated with pollutant concentration?
4. Do these relationships remain informative when spatial and temporal characteristics are considered together?
5. Do pollutant concentrations differ across traffic-exposure groups?
6. Can traffic, spatial and temporal characteristics predict NO₂?
7. Can the same variables predict PM2.5?
8. Which variables contribute most to model predictions?

---

## Study Design

**Study type:** Observational quantitative study

**Study area:** London, United Kingdom

**Primary outcomes:**
- PM2.5 concentration
- NO₂ concentration

**Main analytical components:**
- Data quality assessment
- Data cleaning and preparation
- Exploratory data analysis
- Spatial and traffic exposure analysis
- Statistical hypothesis testing
- Feature engineering
- Group-aware train/test splitting
- Baseline regression
- Machine learning
- Cross-validation and hyperparameter tuning
- Residual and deployment-level diagnostics
- Model explainability

---

## Data Sources

### 1. Breathe London AQMesh Pods

The air-quality data come from the Breathe London AQMesh stationary network published through the London Datastore.

The project uses:
- `meta_data.csv`
- `stationary_data.csv`

The observed period in the working dataset is:

**20 September 2018 to 18 November 2019**

The working dataset contains observations from 106 monitoring locations and 92 unique pod IDs.

Source:

https://data.london.gov.uk/dataset/breathe-london-aqmesh-pods-2r401

### 2. London Atmospheric Emissions Inventory (LAEI) 2022

Traffic exposure variables were derived from the LAEI 2022 traffic-flow datasets.

The project uses:
- `LAEI2022-major-roads-flows-and-speeds.xlsx`
- `LAEI2022-minor-roads-flows-and-speeds.xlsx`

The traffic inventory provides different spatial representations for major and minor roads. Major roads contain road-level traffic counts and speeds, while minor-road data provide grid-level vehicle-kilometre measures.

Source:

https://data.london.gov.uk/dataset/london-atmospheric-emissions-inventory-laei-2022-2lg5g

### Important Temporal Limitation

The air-quality observations cover 2018–2019, while the traffic inventory used in this project represents 2022.

Therefore, traffic variables are treated primarily as **spatial exposure proxies**, not as contemporaneous measurements of traffic conditions during each air-quality observation.

---

## Data Overview

### Air Quality

| Dataset | Shape |
|---|---:|
| Metadata | 181 × 16 before derived duration |
| Stationary observations | 757,357 × 10 |

### Traffic

| Dataset | Shape |
|---|---:|
| Major roads | 79,437 × 18 |
| Minor roads | 3,460 × 20 |

The major and minor road datasets were not concatenated directly because they represent traffic using different spatial structures.

---

## Data Quality

Key quality checks included:

- Duplicate site-time records
- Missing pollutant measurements
- Missing metadata
- Coordinate consistency
- Deployment coverage
- Pollutant-specific metadata matching
- Traffic-data missingness
- Implausible traffic speeds
- Zero-traffic observations
- Temporal gaps
- Monitoring-site deployment structure

No duplicate site-time combinations were identified in the stationary air-quality data.

PM2.5 availability was approximately 75.27%, while NO₂ availability was approximately 86.52%.

Missing observations were handled through the documented preprocessing workflow rather than blanket deletion.

---

## Feature Engineering

The final candidate feature inventory included spatial, site, traffic, temporal and cyclical variables.

### Primary model features

**Spatial**
- latitude
- longitude
- distance_from_road
- height

**Traffic**
- log_minor_total_vkm

**Temporal**
- hour
- day of week
- day of year represented through cyclical transformations
- rush-hour indicators
- weekday indicator
- season

**Categorical**
- monitoring-site type
- ULEZ status
- borough
- season

Cyclical transformations were used for periodic variables such as hour, day of week and day of year.

---

## Train/Test Strategy

A group-aware train/test split was used based on `pod_id_location`.

This was important because ordinary random row splitting could place observations from the same monitoring deployment in both training and test sets.

The final split therefore evaluated whether models could generalize to **unseen monitoring deployments**.

### Final split

**PM2.5**
- Training observations: 449,758
- Test observations: 120,325
- Training deployments: 64
- Test deployments: 16
- Deployment overlap: 0

**NO₂**
- Training observations: 515,385
- Test observations: 139,877
- Training deployments: 80
- Test deployments: 21
- Deployment overlap: 0

---

## Statistical Analysis

The statistical analysis tested associations between traffic-related variables, road distance and pollutant concentrations.

### Results

| Hypothesis | Pollutant | Statistic | p-value | Decision |
|---|---|---:|---:|---|
| Traffic exposure association | NO₂ | -0.1696 | 0.095036 | Fail to reject H₀ |
| Traffic exposure association | PM2.5 | -0.0691 | 0.542379 | Fail to reject H₀ |
| Road distance association | PM2.5 | -0.1211 | 0.300584 | Fail to reject H₀ |
| Road distance association | NO₂ | -0.3334 | 0.001163 | Reject H₀ |
| Traffic groups | PM2.5 | 4.6648 | 0.198054 | Fail to reject H₀ |
| Traffic groups | NO₂ | 1.5133 | 0.679205 | Fail to reject H₀ |

The statistically significant result was the association between road distance and NO₂.

These findings are associations only and are not interpreted as causal effects.

---

## Machine Learning

The modelling stage compared a linear baseline with nonlinear tree-based models.

Models evaluated included:

- Linear Regression
- Random Forest
- Gradient Boosting
- XGBoost
- Tuned Random Forest

### Final Test Performance

| Pollutant | Model | MAE | RMSE | R² |
|---|---|---:|---:|---:|
| PM2.5 | Linear Regression | 7.0560 | 10.7619 | 0.0762 |
| PM2.5 | Random Forest | 3.1921 | 7.4556 | 0.5566 |
| PM2.5 | Gradient Boosting | 5.1623 | 8.2249 | 0.4604 |
| PM2.5 | XGBoost | 3.9002 | 6.6123 | 0.6513 |
| PM2.5 | Tuned Random Forest | 3.1004 | 6.8859 | 0.6218 |
| NO₂ | Linear Regression | 15.4709 | 21.3309 | 0.1230 |
| NO₂ | Random Forest | 9.6668 | 15.0134 | 0.5655 |
| NO₂ | Gradient Boosting | 13.6690 | 19.1431 | 0.2936 |
| NO₂ | XGBoost | 11.4627 | 16.6216 | 0.4675 |
| NO₂ | Tuned Random Forest | 10.4257 | 15.4236 | 0.5415 |

The nonlinear models substantially outperformed the linear baseline.

The tuned Random Forest was optimized using group-aware cross-validation with MAE as the scoring metric.

### Model Selection and Interpretation

Model performance differed across pollutants and evaluation metrics.

For PM2.5, XGBoost produced the highest test R² and lowest RMSE, while the tuned Random Forest produced the lowest MAE. For NO₂, the untuned Random Forest produced the highest test R² and lowest MAE among the evaluated models.

The tuned Random Forest was selected for the subsequent diagnostic and explainability analysis because it was optimized using group-aware cross-validation with MAE as the scoring metric.

These results do not imply that one model was universally superior across all evaluation criteria.

---

## Model Diagnostics

Residual analysis showed that both tuned models had difficulty reproducing the highest pollution concentrations.

For PM2.5:

- Test MAE: 3.1004
- Test RMSE: 6.8859
- Test R²: 0.6218

For NO₂:

- Test MAE: 10.4257
- Test RMSE: 15.4236
- Test R²: 0.5415

Prediction distributions were narrower than the observed distributions, particularly at the upper end.

Deployment-level diagnostics also showed that model performance varied across unseen monitoring deployments.

---

## Explainability

Two complementary approaches were used:

1. Random Forest impurity-based feature importance
2. Joint permutation importance on a reproducible test sample

The permutation analysis jointly permuted:
- cyclical feature pairs
- categorical one-hot groups

This reduced the risk of interpreting individual one-hot columns or one half of a cyclical representation in isolation.

### Dominant Predictive Features

Temporal variables consistently dominated the explainability results.

For both pollutants:
- `day_of_year`
- `hour`
- `day_of_week`

were among the strongest predictors.

For PM2.5, additional contributions came from:
- season
- distance from road
- latitude
- longitude
- monitoring-site type
- traffic exposure

For NO₂, additional contributions included:
- season
- monitoring-site type
- morning rush status
- weekday status
- latitude
- longitude

Traffic-related variables contributed predictive information, but their contribution was smaller than the dominant temporal variables.

Feature importance is interpreted as predictive contribution, not causal influence.

---

## Key Findings

### 1. Temporal structure dominated prediction

Annual, daily and weekly temporal patterns provided the largest share of predictive information for both pollutants.

### 2. Traffic exposure showed limited marginal statistical evidence

The tested traffic exposure measure was not statistically significantly associated with either PM2.5 or NO₂.

### 3. Road distance showed a pollutant-specific relationship

Road distance was statistically associated with NO₂ but not with PM2.5 in the tested relationships.

### 4. Nonlinear models captured substantially more predictive structure

Tree-based models considerably outperformed the linear baseline.

### 5. High pollution levels were harder to predict

Both models showed increased errors toward the upper end of the observed concentration distributions.

### 6. Performance varied by monitoring deployment

Overall test metrics did not fully represent differences in predictive performance across unseen monitoring deployments.

---

## Limitations

The main limitations are:

- Traffic data represent 2022 while air-quality observations represent 2018–2019.
- The observational design does not support causal inference.
- Monitoring coverage was uneven across deployments.
- Pollutant measurements contained missing observations.
- Extreme pollutant concentrations increased prediction difficulty.
- Traffic exposure was represented through spatial matching to LAEI data.
- Major and minor road datasets have different spatial structures.
- Feature importance can be affected by correlated predictors and model characteristics.
- External validation across different years, networks or cities was not performed.

---

## Reproducibility

### Environment

The project was developed and executed in Google Colab.

Core Python libraries include:

- Python
- pandas
- NumPy
- Matplotlib
- SciPy
- scikit-learn
- XGBoost
- openpyxl

### Suggested workflow

1. Open the notebook in Google Colab.
2. Download the required source datasets from the official sources.
3. Place the source files in the working environment.
4. Run the notebook from Chapter 1 in order.
5. Do not bypass the data-quality and group-splitting stages.
6. Use the final evaluation section to reproduce the reported test metrics.

The repository should contain code and documentation rather than large raw datasets where licensing, file size or redistribution restrictions make inclusion inappropriate.

---

## Repository Structure

```text
traffic-related-air-pollution-london/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── traffic_related_air_pollution_london.ipynb
│
├── src/
│   └── README.md
│
├── figures/
│   └── README.md
│
└── data/
    └── README.md
```

Raw datasets should normally be downloaded directly from their official sources rather than committed to the repository.

---

## What This Project Demonstrates

This project demonstrates an end-to-end data science workflow involving:

- Multi-source data integration
- Large-scale tabular data processing
- Data quality assessment
- Missing-data analysis
- Spatial exposure engineering
- Temporal feature engineering
- Statistical hypothesis testing
- Group-aware machine learning
- Cross-validation
- Hyperparameter tuning
- Model comparison
- Residual diagnostics
- Deployment-level validation
- Model explainability
- Research interpretation
- Reproducible analytical documentation

The project is intentionally designed to demonstrate transferable data science skills rather than limiting the analysis to environmental datasets.

---

## Author

**Shedrack Chinonso**

Environmental Technology graduate transitioning into Data Science and Machine Learning, with projects spanning environmental analytics, financial risk, customer analytics, business analytics and data engineering.

GitHub: https://github.com/RackLabz

LinkedIn: https://www.linkedin.com/in/shedrack-chinonso-69058219a

---

## Data Attribution

Air-quality data:
Breathe London / Greater London Authority, published through the London Datastore.

Traffic data:
London Atmospheric Emissions Inventory (LAEI) 2022, Greater London Authority / Transport for London.

Please consult the official dataset pages and their associated licence and documentation before redistributing source data.
