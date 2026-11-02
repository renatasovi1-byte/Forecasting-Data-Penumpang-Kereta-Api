# Forecasting Indonesian Train Passenger Volume: ARIMAX vs SARIMAX

Time-series forecasting of monthly railroad passenger volume in Indonesia (2016–2025), comparing **ARIMAX** and **SARIMAX** with three exogenous variables: a COVID-19 dummy, monthly inflation, and the monthly number of holidays. Built with Python as the undergraduate (D3 Applied Statistics and Computation) thesis project at Universitas Negeri Semarang.

**Best model: SARIMAX (2,2,2)(1,0,1)₁₂** — lower AIC and lower test error than ARIMAX on every accuracy metric. It forecasts **49.5–55.8 million passengers per month for 2026**.

---

## Why this problem

In March 2020, national train ridership fell from **23.4 million to 5.9 million passengers in April 2020, a 74.86% drop in one month** (BPS). A model that relies only on historical patterns cannot explain a structural break of this size. This project tests whether adding external variables (COVID-19 dummy, inflation, holidays) and a seasonal component improves forecasts.

## Data

| Item | Detail |
|---|---|
| Target (Y) | Monthly number of train passengers |
| Exogenous (X) | COVID-19 dummy, monthly inflation, number of monthly holidays |
| Period | January 2016 – December 2025 (120 monthly observations) |
| Split | 96 months training / **24 months testing** |
| Source | Passenger and inflation data from Badan Pusat Statistik (bps.go.id), accessed 30 June 2026. Holiday counts were constructed from the Joint Ministerial Decree (SKB 3 Menteri) on national holidays and collective leave. |

The series shows three phases: pre-pandemic growth (26–39 million/month, 2016–2019), the pandemic collapse and gradual recovery (low of 5.48 million in May 2020), and a full recovery to around 50 million/month (2024–2025).

## Methodology (Box-Jenkins)

1. **Exploratory analysis**: trend, seasonality, and the structural break around March 2020.
2. **Variance stabilization**: Box-Cox transformation (λ = 1.9317).
3. **Mean stationarity**: Augmented Dickey-Fuller test; non-seasonal differencing **d = 2**. This is higher than the d = 1 typical in comparable studies, consistent with the pandemic structural break.
4. **Identification**: ACF/PACF plots suggested low-order AR and MA terms (p, q ∈ {1, 2}) and seasonality at lag 12.
5. **Seasonal differencing decision**: D = 0 was chosen empirically. Lag-12 autocorrelation got worse after seasonal differencing (0.1838 → 0.3783), so the seasonal pattern is captured by seasonal AR/MA terms instead.
6. **Estimation and selection**: all candidate orders were fitted and ranked by AIC, then checked with residual diagnostics.
7. **Diagnostics**: white-noise residuals, homoscedasticity, and multicollinearity (VIF < 10, no multicollinearity among exogenous variables).
8. **Evaluation**: out-of-sample accuracy on the 24-month test set (MAPE, RMSE, MAE).
9. **Forecast**: 12 months ahead (2026) with 95% prediction intervals.

## Results

### Model comparison

| Model | Order | AIC (train) | Test MAPE | Test RMSE |
|---|---|---|---|---|
| ARIMAX | (2,2,2) | 998.39 | 19.49% | 9.25 million |
| **SARIMAX** | **(2,2,2)(1,0,1)₁₂** | **845.18** | **16.37%** | **7.71 million** (16.65% lower than ARIMAX) |

SARIMAX is better on AIC, MAPE, RMSE, and MAE, so the seasonal component adds real forecasting value. A test MAPE of 16.37% falls in the "good" band (10–20%).

### Key finding

The **COVID-19 dummy is statistically significant and reduces passenger volume**, confirming that the pandemic shock needs an explicit exogenous term rather than being absorbed by the historical pattern.

### 2026 forecast

- Monthly forecast range: **49.5 – 55.8 million passengers**.
- Peak: **December 2026, about 55.81 million passengers**, consistent with the Christmas and New Year holiday effect.
- The 95% prediction interval widens from **±5.4 million (January 2026)** to **±17 million (December 2026)** as the horizon grows, so later months are much less certain.

## Limitations

- Residuals are **not normally distributed**, which is common when a large structural break is present. Maximum-likelihood estimates are relatively robust to this (Box et al., 2016), but the prediction intervals should be read with care.
- Forecasting 2026 requires future values of the exogenous variables. Holiday counts are known from the official calendar, but inflation must be assumed. **[ISI: how you set the 2026 inflation values]**
- Only one test window (24 months) was used. Rolling-origin evaluation would give a more stable comparison.
- The seasonal differencing choice (D = 0) was made empirically on lag-12 autocorrelation, not with a formal seasonal unit-root test.

## Future work

- Add more exogenous variables (for example fuel price, tourism indicators, or fares).
- Compare SARIMAX against machine-learning or deep-learning models (for example XGBoost or LSTM).
- Test the ARIMAX–SARIMAX accuracy difference formally with the Diebold-Mariano test.

## Repository structure

```
.
├── data_kereta.xslx/                
├── FORECAST/            
├── FINAL ANALISIS/              # Output (grafik)
└── README.md
```

## How to run

```bash
git clone https://github.com/renatasovi1-byte/Forecasting-Data-Penumpang-Kereta-Api.git
cd Forecasting-Data-Penumpang-Kereta-Api
pip install -r requirements.txt
```

Then open the main notebook/script **[ISI: nama file]** in VS Code (or Jupyter) and run all cells in order. The core libraries are pandas, NumPy, SciPy, statsmodels, and matplotlib. **[ISI: sesuaikan dengan import di kodemu]**

## Author

**Sovi Renata Ortesi Khaelani**
D3 Applied Statistics and Computation, Universitas Negeri Semarang
Supervisor: Amidi, S.Si., M.Pd.
[LinkedIn](https://www.linkedin.com/in/sovirenata) · sovirenata2@gmail.com

## References

- Box, G. E. P., Jenkins, G. M., Reinsel, G. C., & Ljung, G. M. (2016). *Time Series Analysis: Forecasting and Control* (5th ed.). Wiley.
- Badan Pusat Statistik (BPS). Railway passenger and inflation statistics. https://www.bps.go.id
