# Dublinbikes Demand Forecasting

## 1. Project Overview

This project is a time-series analysis and forecasting of bike availability for the Dublinbikes share scheme. The goal was to build a predictive model that can forecast hourly bike demand for a specific station. The model identifies strong daily and weekly patterns and provides a forecast that can be used to optimize station rebalancing and improve customer satisfaction.

## 2. Problem Statement

Dublinbikes stations frequently suffer from two key operational problems:
* **Empty Stations:** Customers arrive during peak times (e.g., morning commute) to find no bikes available.
* **Full Stations:** Customers arrive (e.g., evening commute) to find no empty docks to park their bikes.

These issues lead to frustrated customers and inefficient operations. This project aims to solve this by moving from a *reactive* to a *proactive* operational model using forecasting.

## 3. Data Source

The dataset used is the historical availability data from Dublinbikes, publicly available on Ireland's Open Data portal:
* **Source:** [data.gov.ie](https://data.gov.ie/dataset/dublinbikes-api)
* **Data Used:** All 12 monthly CSV files for 2023 were combined into a single dataset.
* **Station Analyzed:** Smithfield North (STATION ID 42)

## 4. Methodology

1.  **Data Preparation:** The combined 2023 data (`dublinbikes_2023_combined.csv`) was loaded. The `TIME` column was converted to datetime objects, and the dataset was filtered to a single station (ID 42).
2.  **Resampling:** The raw 5-minute data was resampled into a clean hourly series by taking the mean availability for each hour.
3.  **Analysis (EDA):** The hourly time-series was decomposed using `statsmodels` to identify underlying patterns. This clearly revealed:
    * A strong **24-hour (daily) seasonality** tied to the commuter cycle.
    * A distinct **7-day (weekly) seasonality** showing different patterns on weekdays versus weekends.
4.  **Modeling:** A time-series forecast model was built using **Prophet (by Meta)**, as it is specifically designed to handle multiple seasonalities (daily and weekly) and logistic growth (a station has a max capacity, or `cap`).
5.  **Validation:** The model was trained on 11 months of data (Jan-Nov 2023) and validated against a "holdout" test set of 1 month (Dec 2023) that it had never seen before.

## 5. Results & Findings

The model successfully learned the complex patterns of the station:
* **Finding 1 (Daily):** Availability drops sharply after 7 AM (morning commute) and rises steeply after 5 PM (evening commute).
* **Finding 2 (Weekly):** Weekdays show a strong commuter pattern, while weekends have a much gentler, different usage pattern.
* **Performance:** The model's forecast for the unseen December data (orange line) closely tracked the *actual* bike availability (blue line), proving its predictive power.

**Model Performance (December 2023 Test Set)**
* **Mean Absolute Error (MAE):** 5.09 bikes
* **Root Mean Square Error (RMSE):** 6.55 bikes

This means, on average, the model's hourly forecast was off by approximately 5 bikes, which is a strong result for a station with 30-40 bike stands.


<img width="889" height="890" alt="image" src="https://github.com/user-attachments/assets/aa40ac84-6b0a-4f1d-8121-dfec2c8ca2ae" />


<img width="1241" height="545" alt="image" src="https://github.com/user-attachments/assets/dd276c8f-d494-4136-ab8b-103885f4468d" />


*(Add your plot images here in your GitHub repo, e.g., `components_plot.png` and `forecast_test.png`)*

## 6. Business Value

This forecasting model provides **actionable, proactive intelligence**. An operations team can use these 24-hour-ahead forecasts to:
* **Prevent Empty Stations:** Proactively send a truck to add bikes *before* the 8 AM rush.
* **Prevent Full Stations:** Proactively send a truck to remove bikes *before* the 6 PM rush.

This directly improves operational efficiency and customer satisfaction.

## 7. How to Run

1.  Clone this repository.
2.  Ensure you have the combined data file `dublinbikes_2023_combined.csv` in the same directory.
3.  Install the required libraries:
    ```bash
    pip install pandas matplotlib statsmodels prophet sklearn
    ```
4.  Run the Python script:
    ```bash
    python dublinbikes_forecast.py
    ```
