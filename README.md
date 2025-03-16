# Introduction
Walmart is a retail corporation and has a chain of hypermarkets. The data provided is 2,5 years weekly sales data from 45 stores and 81 departments. Walmart’s weekly sales data presents a challenging forecasting task with trend, seasonality, and irregular holiday effects. This repository contains the Walmart Sales Forecast dataset from Kaggle. The goal of this project is to explore historical sales data, understand trends, important features and forecasting of future sales. To address this, we compare two modeling approaches – Exponential Smoothing and Gradient Boosting  – in forecasting Walmart sales. The performance is evaluated using the Weighted Mean Absolute Error (WMAE), which assigns higher penalty to errors during holiday weeks.

## Data Evaluation Metric (WMAE)
Walmart provided historical weekly sales for multiple stores and departments, along with indicators for holiday weeks. The evaluation metric is Weighted Mean Absolute Error (WMAE), which modifies MAE by giving specific weights to each observation: holiday weeks carry 5× weight and non-holiday weeks weight 1​

 In practice, this means forecast errors during major holidays (Super Bowl, Labor Day, Thanksgiving, Christmas, etc.) are five times more costly than errors in regular weeks.
 
![1_VKYKK85ViLYUUjyOWVURfw](https://github.com/user-attachments/assets/b8ce06a8-e333-43b6-80ea-11b8a7f222f1)


Where wi=5wi​=5 for holiday weeks and wi=1wi​=1 . This metric emphasizes accuracy for peak-sales periods. All model comparisons below are based on WMAE – a lower WMAE indicates a more accurate model given the weighted error.

## Link to a data
https://www.kaggle.com/datasets/aslanahmedov/walmart-sales-forecast

## Methodology, Data Understanding, Preprocessing and Cleaning.

- Started by merging pieces of dataset, examining and understanding key features of it. Removed missing values, convert negative values in sales and convert markdown from nan to numeric values, encoded different non numeric features.
- Visualized trends over time to observe trend patterns in sales.
- Explored relationships between features using scatter plots and bar charts.
- Built relationships matrix and feature importance chart.
- Applied and tuned Gradient Boosting and Exponential Smoothing models.
- Evaluated Model performance using Weighted Mean Absolute Error, particularly assigning higher weight to holiday periods.

# Models

## Exponential smoothing
For a time-series approach, I used an exponential smoothing method (specifically Holt-Winters exponential smoothing). This model captures level, trend, and seasonal components by updating estimates with exponentially decreasing weights for older observations.
- I set a seasonal period of 52 weeks to capture annual seasonal patterns. The Holt-Winters method with additive seasonality was applied, which updates a set of 52 seasonal indices along with level and trend at each time step.
- Exponential smoothing methods do not strictly require the series to be stationary (unlike ARIMA) because they can model trends and seasonality directly​. But in some cases applying differencing or log transformation can help the model to perform better. In my case the result on the original data was not optimal (3537 WMAE score), the original data was not stationary. So I decided to apply differencing and see how it will change the result. WMAE went to 1057 instead. Any forecasts made on the differenced series were integrated (cumulatively summed) to revert to original scale.

## Gradient Boosting
For a machine learning approach, I employed a Gradient Boosting Regressor. This model builds an ensemble of decision trees in sequence, where each new tree corrects errors of the previous ensemble.
- Gradient boosting does not require the time series to be stationary. The model can learn a trend or seasonal pattern from the data if provided appropriate features
- The model able to incorporate a wide range of features (holiday indicators, store info, economic metrics, lags of sales, etc.). This flexibility allows the model to learn complex relationships that time series model cant.
- It can capture non-linear effects and interactions between variables. For instance, it can learn different seasonal patterns for different stores or departments by using store/dept as features interacting with week-of-year.


# Results
After performing several test and tuning parameters of both of the models I can draw a summary. In the case of this exact dataset simpler model (Exponential Smoothing) performed much better than a more complex (Gradient Boosting) model. The Exponential Smoothing achieved around 6% of error while the Gradient Boosting showed 16%. This can be explained by a few things.
- Exponential smoothing methods are specifically built to capture level, trend, and seasonality. They update forecasts based on past observations with exponentially decreasing weights. During the exploratory analysis I spotted a clear trend in winter month
- Gradient Boosting has a higher complexity, a lot of hyperparameters and is much more sensitive to tuning.
- Gradient Boosting requires extensive feature engineering, the set of features provided by the dataset might not fit the model well enough, the movement in sales just might not be explained well enough by available features. Smoothing on another hand does not require any features to forecast the prediction and might catch this exact dataset better.


