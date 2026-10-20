# Deposit Modeling: Time-Series Forecasting and Macroeconomic Stress Testing

## Project Overview

This project develops a quantitative framework for forecasting bank deposit balances and evaluating deposit behavior under changing economic conditions. Prepared as a **Corporate Treasury Quantitative Analytics case study for a Bank of America (BofA) Superday**, the analysis combines time-series modeling, macroeconomic variable selection, statistical diagnostics, and scenario-based stress testing.

The objective is to understand how customer deposit behavior responds to interest rates, economic growth, inflation, unemployment, and financial market volatility. These insights are relevant to liquidity risk management, funding strategy, deposit stability assessment, and capital planning.

The framework analyzes three major deposit categories:

- **Non-Interest-Bearing Deposits:** Transaction-oriented deposits that may be relatively insensitive to deposit rates but vulnerable to confidence-driven withdrawals.
- **Non-Maturity Interest-Bearing Deposits:** Interest-bearing accounts without fixed maturities that may respond to changes in market yields and deposit pricing.
- **Time Deposits:** Fixed-maturity deposits, including certificates of deposit (CDs), whose behavior is influenced by rate competitiveness, maturity schedules, and early withdrawals.

The modeling framework uses historical quarterly deposit data and macroeconomic variables to develop deposit forecasts and evaluate potential outflows under alternative economic scenarios.

## Objectives

- Analyze historical trends, seasonality, and structural changes in bank deposits.
- Test deposit series for stationarity and determine appropriate differencing requirements.
- Develop deposit-specific SARIMA/SARIMAX specifications using autocorrelation and partial autocorrelation analysis.
- Identify relevant macroeconomic drivers using Granger causality, distance correlation, and Pearson correlation.
- Incorporate economic variables into forecasting models to capture relationships beyond historical deposit patterns.
- Evaluate model residuals using the Ljung–Box and Jarque–Bera tests.
- Assess deposit behavior under baseline, recessionary, inflationary, and prolonged stagflation scenarios.
- Translate model outputs into insights for liquidity planning, funding stability, and treasury risk management.

## Data Collection and Preparation

### Deposit Data

Historical quarterly deposit data from the Federal Deposit Insurance Corporation (FDIC) covers **1991–2023** and includes:

- Non-interest-bearing deposits
- Non-maturity interest-bearing deposits
- Time deposits

### Macroeconomic and Financial Variables

Historical economic variables cover **1976–2023** and include:

- Real and nominal GDP and growth rates
- Real disposable income growth
- Unemployment rate
- Treasury yields (3-month, 5-year, and 10-year)
- BBB corporate yields, mortgage rates, and prime rates
- Consumer Price Index (CPI) and inflation measures
- Stock market indices and market volatility
- House price indices

The analysis considers lagged economic variables to capture delayed relationships between economic conditions and deposit behavior.

### Data Preparation

The modeling workflow examines:

- Historical trends and quarterly seasonality
- First differences and seasonal differences where appropriate
- Autocorrelation and partial autocorrelation
- Lagged macroeconomic relationships
- Potential structural breaks and abnormal movements during the Global Financial Crisis (2008) and the COVID-19 period (2020–2021)

These checks help distinguish persistent deposit behavior from temporary shocks and changing economic regimes.

## Methodology

### 1. Exploratory Time-Series Analysis

Each deposit category is examined separately to identify its underlying dynamics.

| Deposit Category | Historical Patterns | Modeling Implications |
|---|---|---|
| Non-Interest-Bearing Deposits | Strong long-term growth, pronounced COVID-era acceleration, and quarterly seasonality | Account for trend, persistence, seasonal cash flows, and structural changes |
| Non-Maturity Interest-Bearing Deposits | Significant growth through 2021, larger quarterly fluctuations, and elevated post-COVID volatility | Capture multiple-quarter persistence, short-term shocks, and seasonal changes |
| Time Deposits | Declining balances through 2016–2019 followed by an increase from 2022 | Model deposit momentum, annual seasonality, and changing interest-rate conditions |

The analysis highlights the importance of distinguishing stable historical patterns from structural changes that may weaken forecasting performance during periods of economic stress.

### 2. Stationarity Testing

The Augmented Dickey–Fuller (ADF), Kwiatkowski–Phillips–Schmidt–Shin (KPSS), and Phillips–Perron (PP) tests are used to evaluate stationarity.

- **ADF:** Tests the null hypothesis of a unit root.
- **KPSS:** Tests the null hypothesis of stationarity.
- **PP:** Tests for a unit root while applying corrections for serial correlation and heteroscedasticity.

The following results are reported for first-differenced deposit series:

| Deposit Category | Test | Statistic | P-value | Interpretation |
|---|---|---:|---:|---|
| Non-Interest-Bearing | ADF | -2.59 | 0.094 | Borderline non-stationary |
| Non-Interest-Bearing | KPSS | 0.093 | >0.10 | Supports stationarity |
| Non-Interest-Bearing | PP | -5.06 | <0.001 | Strong evidence of stationarity |
| Non-Maturity Interest-Bearing | ADF | -2.69 | 0.076 | Borderline non-stationary |
| Non-Maturity Interest-Bearing | KPSS | 0.452 | 0.055 | Supports stationarity at the stated 5% critical value |
| Non-Maturity Interest-Bearing | PP | -6.24 | <0.001 | Strong evidence of stationarity |
| Time Deposits | ADF | -3.90 | 0.002 | Strong evidence of stationarity |
| Time Deposits | KPSS | 0.122 | >0.10 | Supports stationarity |
| Time Deposits | PP | -3.24 | 0.018 | Evidence of stationarity |

The combined evidence supports first differencing for the modeling framework, with the final specification depending on each series' autocorrelation structure and seasonal behavior.

### 3. Deposit-Specific SARIMAX Models

Seasonal Autoregressive Integrated Moving Average with Exogenous Variables (SARIMAX) models are used to capture historical persistence, quarterly seasonality, shocks, and macroeconomic relationships.

The general notation is:

`SARIMAX(p, d, q)(P, D, Q, s)`

where:

- `p`: Non-seasonal autoregressive order
- `d`: Non-seasonal differencing order
- `q`: Non-seasonal moving-average order
- `P`: Seasonal autoregressive order
- `D`: Seasonal differencing order
- `Q`: Seasonal moving-average order
- `s`: Seasonal period; `s = 4` for quarterly data

#### Non-Interest-Bearing Deposits

**Selected specification:** `SARIMAX(1,1,0)(2,0,0,4)`

- AR(1) captures quarter-to-quarter persistence in deposit growth.
- First differencing addresses non-stationarity.
- Seasonal AR terms capture recurring quarterly patterns, including potential year-end and tax-related cash flows.
- No moving-average term is included in the selected specification.

#### Non-Maturity Interest-Bearing Deposits

**Selected specification:** `SARIMAX(3,1,1)(0,1,0,4)`

- AR(3) captures persistence across multiple quarters.
- MA(1) captures short-term shocks and their subsequent adjustment.
- First differencing addresses non-seasonal non-stationarity.
- Seasonal differencing captures quarterly seasonal dynamics.

#### Time Deposits

**Selected specification:** `SARIMAX(1,1,0)(1,0,0,4)`

- AR(1) captures deposit momentum.
- Seasonal AR(1) captures annual patterns in quarterly observations.
- First differencing addresses non-stationarity.
- No moving-average terms are included in the selected specification.

These specifications provide a parsimonious starting point for modeling the distinct behavior of transaction deposits, savings-oriented deposits, and fixed-maturity deposits.

### 4. Macroeconomic Variable Selection

A purely univariate time-series model can capture historical patterns but may not adequately explain deposit movements during changing economic regimes. SARIMAX extends the framework by incorporating exogenous economic variables.

Variable selection combines three analytical approaches:

1. **Granger Causality:** Tests whether lagged values of an economic variable provide incremental predictive information about deposit balances or growth.
2. **Distance Correlation:** Identifies potential nonlinear dependence that may not be captured by conventional linear correlation.
3. **Pearson Correlation:** Measures the strength and direction of linear relationships.

The Granger causality decision rule used in the case study is to reject the null hypothesis when the F-statistic p-value is below 0.05.

#### Key Economic Drivers

| Deposit Category | Important Variables Identified | Potential Economic Interpretation |
|---|---|---|
| Non-Interest-Bearing Deposits | 3-month Treasury rate (lag 1), prime rate (lag 1), CPI, GDP growth, disposable income, unemployment | Transaction balances respond to opportunity costs, business activity, household income, and precautionary saving |
| Non-Maturity Interest-Bearing Deposits | 5-year Treasury yield (lag 2), 3-month Treasury rate (lag 2), prime rate (lag 1), CPI, stock market index, house prices, unemployment, VIX | Deposit pricing, yield competition, wealth effects, and risk aversion influence deposit allocation |
| Time Deposits | House Price Index (lag 3), Treasury yields, mortgage rates, BBB corporate yields, CPI, GDP growth, unemployment, VIX | Rate competitiveness, liquidity preferences, household wealth, and economic confidence influence CD demand and renewal behavior |

The selected variables and lags reflect the relationships investigated in the case study. Correlation and Granger causality provide evidence of predictive relationships; they do not, by themselves, establish economic causation.

### 5. SARIMAX Forecasting Framework

The SARIMAX framework combines:

- **Autoregressive dynamics:** Historical deposit behavior and persistence.
- **Differencing:** Transformation of non-stationary series.
- **Moving-average dynamics:** Adjustment to past shocks.
- **Seasonal components:** Recurring quarterly patterns.
- **Exogenous regressors:** Macroeconomic and financial variables.

Conceptually, the model can be represented as:

`Deposit Forecast = Time-Series Dynamics + Economic Drivers + Residual Error`

This approach provides a structured way to evaluate how changes in rates, economic activity, inflation, employment, and market conditions may affect deposit balances.

## Model Diagnostics and Validation

Residual diagnostics are used to assess whether the selected models capture important time-series structure.

### Diagnostic Results

| Deposit Category | Diagnostic | Test Statistic | P-value | Reported Conclusion |
|---|---|---:|---:|---|
| Non-Interest-Bearing | Ljung–Box (4 lags) | — | 0.619 | No significant residual autocorrelation |
| Non-Interest-Bearing | Jarque–Bera | 1.044 | 0.593 | Residual normality not rejected |
| Non-Maturity Interest-Bearing | Ljung–Box (4 lags) | — | 0.509 | No significant residual autocorrelation |
| Non-Maturity Interest-Bearing | Jarque–Bera | 1.869 | 0.393 | Residual normality not rejected |
| Time Deposits | Ljung–Box (4 lags) | — | 0.258 | No significant residual autocorrelation |
| Time Deposits | Jarque–Bera | 0.447 | 0.800 | Residual normality not rejected |

The reported diagnostic p-values exceed the conventional 5% significance threshold, so the tests do not reject their respective null hypotheses.

However, satisfactory residual diagnostics do not establish forecast accuracy. Additional out-of-sample testing, forecast-error metrics, and validation across different economic regimes would be needed to compare predictive performance conclusively.

## Macroeconomic Stress Testing

The final stage evaluates deposit behavior under four illustrative economic scenarios for 2024–2027.

### Scenario Definitions

| Scenario | Economic Environment | Principal Risk |
|---|---|---|
| Supervisory Baseline | Stable growth, soft landing, and gradual rate normalization | Maintaining funding efficiency and deposit stability |
| Supervisory Severely Adverse | Deep recession, elevated unemployment, market stress, and emergency rate cuts | Significant deposit outflows and liquidity pressure |
| Exploratory Conditions A | Moderate recession, persistent inflation, and constrained monetary policy | Yield competition and inflation-driven deposit reallocation |
| Exploratory Conditions B | Prolonged stagflation, high inflation, weak growth, and elevated market stress | Sustained deposit flight and higher funding costs |

The scenarios are designed to illustrate how deposit behavior may change as economic conditions and customer incentives evolve.

### Illustrative Deposit Outflow Assumptions

The following ranges are scenario assumptions from the case study, not empirically validated forecasts or estimates of actual bank-specific outflows.

| Deposit Category | Supervisory Baseline | Supervisory Severely Adverse | Exploratory Conditions A | Exploratory Conditions B |
|---|---:|---:|---:|---:|
| Non-Interest-Bearing Deposits | 5–10% | 15–30% | 10–25% | 20–40% |
| Non-Maturity Interest-Bearing Deposits | 10–20% | 40–60% | 30–50% | 50–70% |
| Time Deposits | <5% | 10–20% | 15–25% | 20–30% |

The assumed outflows vary by deposit type, reflecting differences in customer rate sensitivity, maturity structure, and potential withdrawal behavior.

### Scenario Interpretation by Deposit Category

**Non-Interest-Bearing Deposits**

- Under the baseline, transaction-driven balances remain comparatively sticky.
- Under severe stress, declining confidence and business liquidity needs may increase withdrawals.
- Under persistent inflation or prolonged stagflation, customers may seek alternative assets or draw down cash to meet expenses.

**Non-Maturity Interest-Bearing Deposits**

- Changes in deposit rates can affect the attractiveness of these accounts relative to market alternatives.
- Severe market stress may trigger rapid reallocations into Treasury securities or other perceived safe assets.
- Persistent inflation may increase the incentive to seek higher real returns outside traditional deposit accounts.

**Time Deposits**

- Stable conditions support predictable maturities and renewals.
- Rate changes and liquidity needs can increase early redemptions or reduce renewal rates.
- Persistent inflation may encourage customers to lock in yields initially but can also increase pressure to seek alternative inflation-sensitive investments.

### Implications for Corporate Treasury

The stress-testing framework helps translate quantitative deposit forecasts into practical treasury considerations:

- **Liquidity management:** Estimate potential funding gaps and evaluate the liquidity buffer needed under adverse scenarios.
- **Funding strategy:** Assess the stability of different deposit categories and the potential need for alternative funding.
- **Interest-rate risk:** Evaluate how market rates and deposit pricing affect customer behavior and funding costs.
- **Scenario planning:** Compare deposit sensitivity across recessionary, inflationary, and baseline conditions.
- **Capital and regulatory planning:** Provide quantitative inputs to broader liquidity and capital stress-testing processes.

The scenario analysis is illustrative and should be supplemented with institution-specific deposit data, empirically calibrated runoff assumptions, and appropriate regulatory requirements before use in actual risk management.

## Key Findings

1. **Deposit categories have materially different behavioral characteristics.** Non-interest-bearing deposits exhibit transaction-driven seasonality, non-maturity interest-bearing deposits show greater sensitivity to rate competition and shocks, and time deposits display more predictable maturity-related dynamics.

2. **Time-series structure matters.** Differencing, autoregressive terms, and quarterly seasonal components provide a framework for capturing persistence and recurring deposit patterns.

3. **Macroeconomic drivers add explanatory context.** Interest rates, inflation, GDP, unemployment, financial markets, and housing indicators offer potential predictive information beyond historical deposit balances alone.

4. **Nonlinear relationships warrant consideration.** Distance correlation complements Pearson correlation when screening economic variables for potentially complex relationships with deposits.

5. **Residual diagnostics support the selected specifications.** The reported Ljung–Box and Jarque–Bera tests do not reject their respective null hypotheses at the 5% level, although out-of-sample forecasting performance requires separate validation.

6. **Stress scenarios highlight differentiated liquidity risks.** The illustrative assumptions indicate that non-maturity interest-bearing deposits may face greater outflow pressure than time deposits, particularly under severe or prolonged economic stress.

## Limitations and Future Work

Potential extensions to the framework include:

- Perform rolling-window and out-of-sample forecast validation.
- Compare SARIMAX specifications using RMSE, MAE, and other forecast-error measures.
- Test the sensitivity of results to alternative lag structures and macroeconomic variable selections.
- Evaluate structural breaks and regime changes, particularly around the Global Financial Crisis and COVID-19.
- Calibrate deposit runoff assumptions using institution-specific data and observed customer behavior.
- Incorporate deposit pricing, customer segmentation, concentration, and uninsured-deposit exposure where data permit.
- Evaluate alternative stress scenarios and the implications for liquidity buffers, funding costs, and net interest margins.
- Conduct robustness checks to distinguish predictive associations from causal economic mechanisms.

## Repository and Reproducibility

This repository documents the deposit-modeling case study and its analytical framework. The implementation files, input data, and execution instructions should be consulted directly in the repository when reproducing the analysis.

For reproducible results, users should document the data sources, transformations, model specifications, estimation windows, exogenous-variable assumptions, software dependencies, and scenario inputs used in each run.

## Author

**Divija Rao Balasankula**

Quantitative Analytics | Financial Modeling | Time-Series Analysis | Macroeconomic Stress Testing

## Disclaimer

This project is for educational and analytical purposes. The forecasts, relationships, and scenario-based outflow assumptions are illustrative and should not be interpreted as actual Bank of America forecasts, proprietary bank models, regulatory determinations, or investment advice.

The case study does not establish that the models have been validated for production use. Any application to real-world liquidity risk management or regulatory stress testing would require appropriate data governance, institution-specific calibration, independent validation, and compliance with applicable requirements.
