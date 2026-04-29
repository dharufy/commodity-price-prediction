# Commodity Procurement Under Price Uncertainty

Decision-support notebook for a procurement manager: under current market conditions, is it better to lock in today's cooking oil price or wait 30 days?

It focuses on a single commodity:
- Commodity: Minyak Goreng Curah (bulk cooking oil)
- Market: DKI Jakarta
- Period: April 2022 - September 2024

The public repo includes only the minimal data files needed to run that notebook:
- `Harga Bahan Pangan/train/Minyak Goreng Curah.csv`
- `Global Commodity Price/Crude Oil WTI Futures Historical Data.csv`
- `Mata Uang/USDIDR=X.csv`

## Approach

- LightGBM forecasting model with lag features, rolling statistics, calendar features, and a Ramadan indicator
- Volatility regime classification from 30-day rolling standard deviation
- 30-day recursive forecast built by chaining one-step predictions
- Decision layer that combines:
  - forecasted 30-day price direction
  - current volatility regime
  - residual-based scenario bounds

The notebook translates the forecast into a procurement recommendation:
- `BUY NOW`
- `DELAY`
- `SPLIT PURCHASE`

## Key Result

On the July-September 2024 holdout:
- Validation MAPE: about `1.70%`
- Validation MAE: about `IDR 282/kg`

In the final September 2024 decision snapshot, the notebook lands on:
- Recommendation: `SPLIT PURCHASE`

That conclusion comes from a modestly negative 30-day model forecast combined with a still-high volatility regime.

## Run

```bash
pip install -r requirements.txt
jupyter notebook notebooks/main_project.ipynb
```

## Limitations

- Single market / single commodity: this notebook does not claim cross-province or multi-commodity generalisation.
- WTI signal: included as an observed correlated feature, not as a claimed causal driver.
- 30-day forecast uncertainty: the recursive setup compounds error, so the day-30 scenario range is indicative, not precise.
- Decision layer: simplified to a small set of procurement actions; it does not model inventory holding cost, staggered purchasing, or supplier contract constraints.
