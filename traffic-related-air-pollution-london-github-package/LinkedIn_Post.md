I recently completed a full data science project on traffic-related air pollution exposure in London.

The project combined more than 757,000 air-quality observations with monitoring-site metadata and London traffic data.

Instead of stopping at exploratory analysis, I built the project as a complete research workflow:

• Data quality assessment
• Multi-source data integration
• Spatial and traffic exposure engineering
• Statistical hypothesis testing
• Group-aware train/test splitting
• Regression and machine learning
• Cross-validation and hyperparameter tuning
• Residual diagnostics
• Deployment-level validation
• Model explainability

One of the most important decisions was how to split the data.

Because the same monitoring deployment can contain thousands of observations, a normal random row split could make the model look better than it really is. I therefore split the data by monitoring deployment so that the test set contained locations the model had not seen during training.

The final tuned Random Forest achieved:

PM2.5
MAE: 3.10
RMSE: 6.89
R²: 0.622

NO₂
MAE: 10.43
RMSE: 15.42
R²: 0.542

The results were not simply about traffic.

Temporal variables such as day of year, hour and day of week were the strongest predictive features. Traffic exposure contributed useful information, but its predictive contribution was smaller than the dominant temporal structure.

The statistical analysis also showed that the tested traffic exposure measure was not statistically significant for either pollutant, while road distance showed a statistically significant association with NO₂.

That distinction was important.

A variable can contribute to prediction without demonstrating a statistically significant marginal association, and neither result should automatically be interpreted as causal.

The project reinforced something I value in data science: good modelling is not just about getting a high score. It is about designing the experiment correctly, understanding what the model learned, checking where it fails, and being honest about what the data can and cannot support.

Project: Traffic-Related Air Pollution Exposure in London
GitHub: https://github.com/RackLabz
