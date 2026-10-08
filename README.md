# project-1
[README.md](https://github.com/user-attachments/files/33205799/README.md)
# RoadForecast: Traffic Congestion Forecasting

> A weather forecast for traffic jams. Predict congestion before it forms and find the best hour to leave.

**Live demo:** _add your link here_

## The problem

Commuters lose hours every week to jams they cannot predict. Employers and city authorities usually see congestion only after roads have already choked, and most people travel at the same time because they have no reliable way to know a better time.

## The idea

RoadForecast predicts hourly traffic volume and recommends the least congested departure hour within a commuter's window. For employers and cities, the same forecasts can guide staggered shifts, carpool planning and hotspot monitoring.

See the Lean Canvas, concept note and presentation for the full business model (customers, channels, revenue, costs and metrics).

## Features

- Hourly traffic volume forecasting using gradient boosting
- Leave-time recommender for a chosen day, month, weather and commute window
- Light / Moderate / Heavy congestion labels
- Streamlit app, plus a notebook with the full analysis

## Dataset

[Metro Interstate Traffic Volume](https://archive.ics.uci.edu/dataset/492/metro+interstate+traffic+volume) (UCI Machine Learning Repository): hourly traffic volume on the I-94 interstate in Minnesota, USA, from 2012 to 2018, with weather and holiday information.

Cleaning steps in the notebook:
- Removed duplicate timestamps (one row per hour)
- Placed the data on a regular hourly grid (about 12,000 hours are missing in the raw data)
- Removed temperature sensor errors (0 K)
- Built lag features (traffic 24 hours and 168 hours earlier) for the day-ahead model

## Method

1. Time-based split: train on the earlier 80% of the timeline and test on the latest 20% (no random split, to avoid leaking future information).
2. **Baseline:** average volume for the same weekday and hour.
3. **Planning model:** gradient boosting on hour, weekday, month and weather. Used by the app, so it works for any future day.
4. **Day-ahead model:** the same features plus traffic at the same hour yesterday and last week.

## Results (test set)

| Model | Average error (vehicles/hour) | R² |
|---|---|---|
| Baseline (weekday + hour average) | 278 | 0.937 |
| Planning model | 262 | 0.945 |
| Day-ahead model | 251 | 0.947 |

Daily patterns are already strong, so the baseline is hard to beat. The models improve on it modestly (about 6% for the planning model and 10% for the day-ahead model).

## Run it locally

```bash
git clone <your-repo-url>
cd <your-repo-folder>
pip install -r requirements.txt
streamlit run app.py
```

To explore the analysis, open `traffic_forecast.ipynb` in Jupyter or Google Colab.

## Project structure

```
app.py                              Streamlit leave-time app
traffic_forecast.ipynb              Cleaning, modelling and evaluation
Metro_Interstate_Traffic_Volume.csv Dataset
requirements.txt                    Python dependencies
```

## Limitations

- Prototype trained on one US highway, not on local city data
- Hourly resolution, so it recommends an hour rather than an exact minute
- Weather is simplified in the app (Clear, Clouds, Rain, Snow)
- The Light / Moderate / Heavy thresholds are a simple choice based on the dataset's peak traffic (under 40%, 40-75%, over 75% of the 95th percentile)

## Future work

- Retrain on local data from a pilot with an employer or campus
- Finer time resolution and live traffic feeds
- Employer tools: staggered-shift suggestions and carpool matching
- Dashboard for city traffic authorities

## Tech stack

Python, pandas, scikit-learn, matplotlib, Streamlit
