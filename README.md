# unibo-svar
Structural Analysis of The Phillips Curve in Python

## Table of Contents
- [Overview](#overview)
- [Installation](#installation)
- [Modules](#modules)
- [Requirements](#requirements)
- [References](#references)
- [License](#license)

## Overview
This repository contains the report and companion code for *A Structural VAR Analysis of The Phillips Curve* (University of Bologna, November 6, 2024) by Shahriyar Majidi, Ekaterina Nemchenko and Jelena Cesnokova.

The study analyzes the relationship between unemployment and inflation in the U.S. economy using a structural vector autoregressive (SVAR) model of four quarterly variables: the Federal Funds Rate, core PCE, the unemployment rate and real GDP. After transforming the series to achieve stationarity, a reduced-form VAR is estimated, identified via a Cholesky decomposition, and analyzed through dynamic multipliers and impulse-response functions. The results show a short-lived, small negative relationship between inflation and unemployment that dissipates after about 10 quarters.

## Installation
```bash
git clone https://github.com/donn-majidi/unibo-svar.git
cd unibo-svar
python -m venv .venv
source .venv/bin/activate  # on Windows: .venv\Scripts\activate
pip install numpy pandas matplotlib statsmodels jupyter
jupyter notebook Phillips_curve.ipynb
```

See [Requirements](#requirements) for the list of required packages. The notebook reads the data from `./data/2024-09.csv`, so run it from the repository root.

## Modules

### Repository contents
| Path | Description |
|------|-------------|
| `A_Structural_VAR_Analysis_of_The_Phillips_Curve.pdf` | The main report. |
| `Phillips_curve.ipynb` | Companion code reproducing the tables and figures of the report. |
| `data/2024-09.csv` | FRED-QD quarterly macroeconomic database (September 2024 vintage). |
| `LICENSE` | GNU General Public License v3.0. |

### `Phillips_curve.ipynb`
The notebook follows the structure of the report:

1. **Data Pre-Processing**
   - Loads the FRED-QD dataset (248 series) and keeps four variables: `GDPC1` (real GDP), `PCEPILFE` (core PCE), `UNRATE` (unemployment rate) and `FEDFUNDS` (Federal Funds Rate).
   - Restricts the sample to the period after the 1985Q1 structural break and up to 2019Q4, before the COVID-19 outbreak.
   - Applies the stationarity-inducing transformations below, and plots the transformed series with recession periods shaded.

   | Original variable | Symbol | Transformed variable | Transformation |
   |-------------------|--------|----------------------|----------------|
   | Federal Funds Rate | r<sub>t</sub> | i<sub>t</sub> | First difference |
   | Unemployment Rate | u<sub>t</sub> | w<sub>t</sub> | First difference |
   | Real GDP | Y<sub>t</sub> | y<sub>t</sub> | Log difference |
   | Core PCE | Π<sub>t</sub> | π<sub>t</sub> | Second-order log difference |

   The helper `adf_test(series, title='')` wraps the Augmented Dickey-Fuller test (lag length chosen by AIC) and returns the test statistic, p-value, number of lags, number of observations and critical values as a `pandas.Series`. All transformed series reject the unit-root null at the 5% level.

2. **Reduced Form Analysis**
   - Estimates a VAR on the variables ordered as (`fedfunds`, `infl`, `gdp`, `unrate`), from most to least exogenous.
   - Selects the lag order with the AIC (2 lags). The BIC is not used because the residuals of the model it selects are serially correlated.
   - Checks the residuals with partial autocorrelation plots and the Ljung-Box test (12 lags).

3. **Structural Analysis**
   - Computes the Cholesky factor `P` of the residual covariance matrix, which identifies the structural shocks.
   - Builds the companion matrix `J` of the VAR, verifies that all its eigenvalues lie inside the unit circle, and computes the dynamic multipliers C<sub>k</sub> = (R<sub>1</sub><sup>T</sup> J<sup>k</sup> R<sub>1</sub>) P of unemployment, where R<sub>1</sub> is the selection matrix.

4. **Impulse-Response Analysis**
   - Plots the orthogonalized impulse-response functions up to 20 quarters, using a fixed seed (1776) for reproducible confidence bands.

### Main findings
- A structural shock to the Federal Funds Rate, inflation or GDP growth has a negative impact on unemployment, implying a trade-off in the short run.
- The negative effect of inflation on unemployment is small in magnitude, becomes statistically insignificant after the second quarter, and is fully absorbed after about 10 quarters, when unemployment returns to its natural rate.

## Requirements
- [`numpy`](https://numpy.org/)
- [`pandas`](https://pandas.pydata.org/)
- [`matplotlib`](https://matplotlib.org/)
- [`statsmodels`](https://www.statsmodels.org/)
- [`jupyter`](https://jupyter.org/) (to run the notebook)

## References
- Sims, C. A. (1980). Macroeconomics and Reality. *Econometrica*, 48(1), 1–48.
- Hamilton, J. D. (1994). *Time Series Analysis*. Princeton University Press.
- McCracken, M. W., & Ng, S. (2015). FRED-MD: A Monthly Database for Macroeconomic Research. *Federal Reserve Bank of St. Louis Working Paper 2015-012*.
- McCracken, M. W., & Ng, S. (2020). FRED-QD: A Quarterly Database for Macroeconomic Research. *Federal Reserve Bank of St. Louis Working Paper 2020-005*.
- Romer, D. (2012). *Advanced Macroeconomics*. McGraw-Hill.
- Dickey, D., & Fuller, W. (1979). Distribution of the Estimators for Autoregressive Time Series With a Unit Root. *Journal of the American Statistical Association*, 74, 427–431.
- Box, G. E. P., & Pierce, D. A. (1970). Distribution of Residual Autocorrelations in Autoregressive-Integrated Moving Average Time Series Models. *Journal of the American Statistical Association*, 65(332), 1509–1526.
- Granger, C. W. J. (1969). Investigating Causal Relations by Econometric Models and Cross-Spectral Methods. *Econometrica*, 37(3), 424–438.
- Phillips, A. W. (1958). The Relation Between Unemployment and the Rate of Change of Money Wage Rates in the United Kingdom, 1861–1957. *Economica*, 25, 283–299.
- Lucas, R. E. (1976). Econometric policy evaluation: A critique. *Carnegie-Rochester Conference Series on Public Policy*, 1, 19–46.

## License
This project is licensed under the GNU General Public License v3.0. See the [LICENSE](LICENSE) file for details.
