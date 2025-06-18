# Electricity Demand Forecasting Project

## Introduction
Electricity demand forecasting is a key component of effective power system management, ensuring that sufficient power is available to meet requirements, while avoiding over-production and waste. Demand forecasting applies to a range of time scales, from short-term forecasting (hours to days ahead), to long-term forecasting (years ahead). Short-term forecasting will inform the active management of power systems, while long-term forecasting will inform planning and infrastructure investments more broadly.

This project aims to build (time series) forecasting models using machine learning, to predict electricity demand for the Netherlands on a short-term (day ahead) time scale, at 15 minute intervals.

The project is split over the following notebooks:
- [Part 1: Linear models](https://github.com/pdmasson/electricity-demand-forecasting/blob/main/Electricity_Demand_Forecast_Part_1.ipynb)
- [Part 2: RNN models](https://github.com/pdmasson/electricity-demand-forecasting/blob/main/Electricity_Demand_Forecast_Part_2.ipynb)
- [Functions notebook](https://github.com/pdmasson/electricity-demand-forecasting/blob/main/Functions.ipynb)

The majority of the work is performed in Part 1. Part 2 builds on this further through the exploration of RNN models. The Functions notebook is included to summarise helper functions from Part 1 that are re-used in Part 2.

## Data
### Electricity Demand Data
Electricity demand data for the Netherlands is obtained from the [ENTSO-E Transparency Platform](https://transparency.entsoe.eu/). The ENTSO-E Transparency Platform manages the central collection and publication of electricity generation, transportation and consumption data and information for the pan-European market. Data is retrieved from the _Total Load - Day Ahead/Actual_ view, per year for the period 2015 - 2025 (up to 30/04/2025), at a 15-minute interval.

### Weather Data
The relationship between various weather fields and elecricity demand is analysed to explore whether these can be included as feature variables. Weather data is obtained from the [Netherlands Meteorological Institute (KNMI)](https://www.knmi.nl/nederland-nu/klimatologie/uurgegevens).

For predictive periods, forecast weather data (instead of historical weather data) will be used. This is retrieved from the [Open Meteo API](https://open-meteo.com/en/docs/historical-forecast-api), which is linked to KNMI data.

## EDA and Feature Selection
The evaluation metric applied is **Mean Absolute Error (MAE)**. This is due to the consistent range of electricity demand data and the expected prediction values in relation to these. In the final assessment, MAE percentage deviations also included on a total basis.

The following (potential) feature variables were assessed:
- Lagging timeseries data: daily values corresponding to the same time interval were the most relevant, when included in weekly batches due to the high correlation of the corresponding day of week and time of day value. We determined that 3 weeks of lagging data was optimal.
- Weekday/holiday indicators: adding the public holiday feature improves training and validation MAE, while adding the weekday features does not, likely due to this information already being included in lagging data. Only the holiday feature was included.
- Weather fields: identified correlated weather fields were Q - global radiation, SQ - sunshine duration, U - relative humidity, and T - temperature. These did not yield improved model performance and were not included.

## Model Selection and Evaluation
### Part 1 - Linear Models
We assessed the following Linear Models:
- Linear Regression
- SGD Regressor
- Lasso, Ridge, ElasticNet Regression

The Linear Regression model achieved the best performance on the training/validation data. Thereafter, we analysed the residuals and built a hybrid model combining the Linear Regression model and an XGBoost Regressor. This further improved model performance. Both the Linear Regression model and Hybrid model were included in our final analysis.

<img src="https://github.com/pdmasson/electricity-demand-forecasting/blob/main/images/linear-model.png" width=450 />

### Part 2 - Recurrent Neural Networks
We assessed the following RNN models:
- Simple RNN
- Deep RNN
- Long Short-Term Memory (LSTM)
- Gated Recurrent Unit (GRU)

The GRU model achieved the best performance on the training/validation data.

<img src="https://github.com/pdmasson/electricity-demand-forecasting/blob/main/images/rnn-models.png" width=400 />

### Conclusion
Each of the RNN models performed well, with the GRU model achieving the best performance, predicting day ahead electricity demand well, with deviations mostly driven by the variability of daily peak profiles, which cannot be fully captured by lagging data. The Linear Regression model also performed well, especially when considering its simplicity, efficiency of use and explainability. The addition of the XGBoost Regressor to the Linear Regression model (as a hybrid model) further improved performance.

![](https://github.com/pdmasson/electricity-demand-forecasting/blob/main/images/gru-predictions.png)

Further investigation should focus on identifying other potential feature variables, with the goal of capturing more of the daily variability in the model forecast.








