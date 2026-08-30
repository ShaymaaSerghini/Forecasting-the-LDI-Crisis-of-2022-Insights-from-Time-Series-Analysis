## Forecasting the 2022 UK LDI Crisis with Time-Series Models

A time-series analysis of the **2022 UK gilt / Liability-Driven Investment (LDI) crisis**, using **2,779 Bank of England market-day observations from 2014–2024**.

The project investigates the behaviour of the **10-year nominal zero-coupon gilt yield** around two key events:

- **23 September 2022** — UK mini-budget
- **28 September 2022** — Bank of England emergency gilt-market intervention

The analysis combines **conditional-mean forecasting, conditional-volatility modelling and multivariate VAR analysis** to construct two fixed-origin statistical counterfactuals:

1. **No-crisis baseline** — information frozen on 22 September.
2. **No-BoE-intervention baseline** — information frozen on 27 September.

> The counterfactual gaps are model-based statistical comparisons, not causal estimates.

**Tools:** Python · pandas · NumPy · statsmodels · ARCH · matplotlib

---

### Key Results

- By **27 September**, the actual 10-year gilt yield was approximately **88–89 bps above** the univariate no-crisis counterfactual.
- The VAR produced a similar no-crisis gap of **86.22 bps**.
- Pre-crisis volatility models expected only around **8–9 bps** of conditional volatility at the start of the crisis, while realised innovations reached approximately **25–38 bps**.
- On **28 September**, the observed 10-year yield was approximately **38–40 bps below** the univariate no-BoE-intervention forecasts.
- The VAR no-BoE counterfactual produced a gap of **−44.02 bps** on 28 September and **−57.38 bps** on 4 October.
- More complicated conditional-mean models did **not materially outperform a Random Walk benchmark**.
- VAR results suggest that short-run predictive relationships were considerably stronger **within the gilt yield curve** than between sterling and gilt yields.

<p align="center">
  <img src="assets/01_crisis_window.png" width="850">
</p>

---

# Conditional Mean Models

The conditional-mean analysis compared:

- Random Walk
- AR(2)
- MA(2)
- ARMA(2,2)
- ARIMA(2,1,2)

The models were evaluated using both **fixed-origin validation** and **rolling one-step-ahead validation**.

| Model | Fixed MAE | Fixed RMSE | Rolling MAE | Rolling RMSE |
|---|---:|---:|---:|---:|
| Random Walk | 42.20 bps | **47.30 bps** | **7.04 bps** | **8.51 bps** |
| AR(2) | 42.13 | 47.65 | 7.07 | 8.58 |
| MA(2) | **42.12** | 47.66 | 7.07 | 8.58 |
| ARMA(2,2) | 42.44 | 47.70 | 7.09 | 8.55 |
| ARIMA(2,1,2) | 42.26 | 47.32 | 7.11 | 8.60 |

The main result was that **model complexity provided almost no forecasting advantage over the Random Walk benchmark**.

Rolling one-step forecasts were much more accurate because recent market information was continually incorporated. This showed that **updating the information set mattered considerably more than increasing model complexity**.

<p align="center">
  <img src="assets/02_level_and_changes.png" width="850">
</p>

## No-Crisis Counterfactual

The models were refitted using all available observations through **22 September 2022** and then forecast forward without receiving crisis-period observations.

All five models remained close to approximately **3.54–3.56%**.

The actual 10-year yield reached:

- 23 Sep: **3.8426%**
- 26 Sep: **4.1817%**
- 27 Sep: **4.4341%**

This produced an actual-minus-counterfactual gap of approximately **88–89 basis points by 27 September**.

<p align="center">
  <img src="assets/03_no_crisis_mean_counterfactual.png" width="850">
</p>

## No-BoE-Intervention Counterfactual

The models were next refitted through **27 September**, allowing them to observe the crisis but not the Bank of England intervention period.

On **28 September**, the models forecast approximately **4.43–4.46%**, while the observed yield was **4.0517%**.

The immediate model-based gap was therefore approximately:

**−38 to −40 basis points**

The forecasts became increasingly model-dependent at longer horizons, so the strongest result is concentrated around the immediate post-intervention period.

<p align="center">
  <img src="assets/04_no_boe_mean_counterfactual.png" width="850">
</p>

---

# Conditional Volatility Models

The residuals from the conditional-mean models showed strong evidence of **volatility clustering**.

ARCH-LM tests strongly rejected the null of no ARCH effects, motivating dedicated conditional-volatility models.

The models compared were:

- ARCH(7)
- GARCH(1,1)
- EGARCH(1,1,1)
- GJR-GARCH(1,1,1)
- TARCH(1,1,1)

All models used **Student-t innovations** to account for heavy-tailed residuals.

| Model | Fixed QLIKE | Rolling QLIKE |
|---|---:|---:|
| ARCH(7) | 6.106 | 5.503 |
| GARCH(1,1) | 5.879 | 5.416 |
| EGARCH(1,1,1) | 5.638 | 5.349 |
| GJR-GARCH(1,1,1) | 5.876 | 5.405 |
| TARCH(1,1,1) | **5.491** | **5.340** |

**TARCH achieved the lowest QLIKE under both validation schemes**, although no single volatility specification dominated every diagnostic.

ARCH, GARCH and GJR-GARCH produced cleaner residual diagnostics, so the full set of models was retained for robustness.

## Crisis Volatility Shock

Using information available through **22 September**, the models predicted only approximately **7.6–8.9 bps** of conditional volatility at the beginning of the crisis.

Realised innovation magnitudes were substantially larger:

| Date | Realised Innovation |
|---|---:|
| 23 Sep | 29.75 bps |
| 26 Sep | 34.32 bps |
| 27 Sep | 25.19 bps |
| 28 Sep | 38.27 bps |

The crisis therefore represented not only a large movement in the yield level but also an **extreme volatility shock relative to the pre-crisis state**.

<p align="center">
  <img src="assets/05_no_crisis_volatility.png" width="850">
</p>

## No-BoE Volatility Counterfactual

After refitting through **27 September**, the volatility models correctly recognised that the market was already stressed.

Forecast volatility for 28 September increased to approximately **12.9–17.7 bps**.

However, the realised innovation was approximately **38.83 bps**, leaving a model-based volatility gap of approximately:

**21–26 bps**

The post-intervention period was not uniformly calm. Shocks around **10 October** and **13 October** again exceeded the volatility forecasts from every model.

<p align="center">
  <img src="assets/06_no_boe_volatility_gap.png" width="850">
</p>

---

# Vector Autoregression

A multivariate VAR was used to study relationships between:

- Sterling–US dollar returns
- 5-year gilt-yield changes
- 10-year gilt-yield changes
- 20-year gilt-yield changes

AIC selected **VAR(3)**.

## Forecast Performance

| Validation | MAE | RMSE |
|---|---:|---:|
| Fixed origin | 42.44 bps | 47.67 bps |
| Rolling one-step | 7.30 bps | 8.74 bps |

Like the univariate models, the VAR performed substantially better when continuously updated.

However, adding more variables did **not materially improve long-horizon forecasting accuracy**.

## Granger Predictability

The strongest predictive relationships were within the gilt curve.

- **5y → 10y:** highly significant, `p < 0.001`
- **Sterling → 10y:** not significant, `p = 0.238`
- **10y → Sterling:** not significant, `p = 0.164`
- **20y → 10y:** borderline, `p = 0.052`

This suggests that short-run 10-year yield dynamics were more closely linked to movements elsewhere on the gilt curve than to sterling movements.

## Impulse Responses and FEVD

Reduced-form impulse responses also showed stronger interactions among the **5-, 10- and 20-year gilt yields**.

At horizon 9, the FEVD attributed approximately:

### Sterling

- Own shock: **99.3%**
- Other variables combined: less than 1%

### 10-Year Yield

- 5-year shock: **86.9%**
- Own shock: **8.5%**
- Sterling shock: **4.2%**
- 20-year shock: **0.4%**

These FEVD values depend on the chosen Cholesky ordering and therefore should be interpreted as **descriptive rather than causal**.

<p align="center">
  <img src="assets/07_var_fevd.png" width="850">
</p>

## VAR Counterfactuals

The VAR no-crisis counterfactual produced a 10-year yield of approximately **3.5719% on 27 September**, compared with the actual **4.4341%**.

This produced a gap of:

**+86.22 bps**

This was very close to the **88–89 bps** result from the univariate models.

For the no-BoE exercise, the VAR counterfactual remained approximately **4.48–4.51%**.

Selected results:

| Date | Actual | VAR Counterfactual | Gap |
|---|---:|---:|---:|
| 28 Sep | 4.0517% | 4.4919% | **−44.02 bps** |
| 29 Sep | 4.1210% | 4.5119% | −39.09 bps |
| 3 Oct | 3.9766% | 4.4848% | −50.82 bps |
| 4 Oct | 3.9036% | 4.4774% | **−57.38 bps** |
| 10 Oct | 4.5445% | 4.4777% | +6.68 bps |
| 14 Oct | 4.3868% | 4.4805% | −9.37 bps |

<p align="center">
  <img src="assets/08_var_no_boe_counterfactual.png" width="850">
</p>

---

# Summary

Three results were particularly consistent across the project.

### 1. The mini-budget period was far outside the pre-crisis statistical baseline

By 27 September, the actual 10-year yield was around **86–89 basis points above** the paths predicted using only pre-crisis information.

### 2. The crisis represented an extreme volatility shock

ARCH/GARCH-family models expected only around **8–9 bps** of volatility at the beginning of the crisis, while realised innovations reached approximately **25–38 bps**.

### 3. The observed yield immediately after the BoE intervention was materially below the no-intervention baseline

On 28 September:

- Univariate models: approximately **−38 to −40 bps**
- VAR: **−44.02 bps**

The later period was more mixed, so the strongest cross-model evidence is concentrated in the immediate post-intervention window.

The project does **not** claim that these differences were caused solely by the mini-budget or the Bank of England intervention. They are statistical counterfactual comparisons constructed under strict information constraints.

---

# Applying What I Learned

The main lesson from this project was that successful time-series modelling is not simply about choosing the most sophisticated model.

The quality of the analysis depends heavily on **how the forecasting problem is defined, how information is restricted and how the model is validated**.

In future quantitative projects, I would reuse the same workflow:

- examine the statistical properties of the data before fitting models;
- distinguish between **conditional mean and conditional volatility**;
- use simple benchmarks before introducing more complex models;
- compare **fixed-origin and rolling validation**;
- inspect residual behaviour rather than relying only on forecast accuracy;
- prevent **look-ahead bias and information leakage**;
- test several plausible specifications rather than relying on a single model;
- treat Granger causality, impulse responses and FEVD as predictive tools unless structural identification is justified;
- separate statistical counterfactual evidence from causal claims.

The project gave me a broader workflow that is directly transferable to quantitative finance, risk modelling and other computational problems:

**define the problem → build the data pipeline → test assumptions → fit the model → backtest under realistic information constraints → diagnose the model → stress-test the conclusions → interpret the results.**

---

# Natural Extensions

A natural extension would be to introduce economically relevant exogenous variables using **ARIMAX**.

Potential predictors include:

- **2-year UK gilt yield**
- **SONIA**
- Overnight Index Swap rates
- Bank Rate expectations
- inflation expectations
- liquidity measures

The 2-year gilt and SONIA would be especially interesting because they provide information about **short-term monetary-policy expectations**, which may help explain movements further along the gilt curve.

Any ARIMAX counterfactual would need to forecast the future exogenous variables rather than using their realised values, otherwise the exercise would introduce look-ahead bias.

I would also extend the project using neural-network models:

- **LSTM** — capturing nonlinear temporal dependencies;
- **1D CNN** — detecting short-lived local patterns and market shocks;
- **AR-LSTM hybrid models** — combining a linear autoregressive component with an LSTM to model nonlinear residual structure.

These models would only be considered improvements if they outperform the existing **Random Walk, ARIMA, GARCH-family and VAR benchmarks under strict time-based out-of-sample validation**.

---

# Master's Thesis

This project was developed from my **Master's thesis, _Forecasting the LDI Crisis of 2022: Insights from Time Series Analysis_**, completed at the **School of Mathematical Sciences, Queen Mary University of London**.

The project was subsequently extended in Python to include a more rigorous time-series workflow covering:

**conditional-mean forecasting → conditional-volatility modelling → multivariate VAR analysis → fixed-origin counterfactual forecasting.**
