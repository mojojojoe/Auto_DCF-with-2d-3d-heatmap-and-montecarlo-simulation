Here is the complete `README.md` incorporating everything you've requested — project overview, methodology, DCF formulas, WACC logic, sensitivity/Monte Carlo analysis, data freshness table, and clear structure:

---

# AutoDCF: End-to-End Discounted Cash Flow Valuation with Sensitivity and Monte Carlo Simulation

**AutoDCF** is a Python-based valuation toolkit that automates Discounted Cash Flow (DCF) analysis using up-to-date market and financial data. It supports comprehensive valuation workflows with consistent cost-of-capital modeling, sensitivity diagnostics, and probabilistic valuation via Monte Carlo simulation.

Includes a full working example for **Apple Inc. (AAPL)**.

---

## Features

### 1. DCF Valuation Pipeline

The model implements a multi-year forecast of Free Cash Flow to the Firm (FCFF), followed by terminal value estimation and discounting to present value:

**FCFF formula:**

```
FCFF = EBIT × (1 - Tax Rate) + Depreciation - CapEx - ΔWorking Capital
```

Each variable is forecast using:

* EBIT, Depreciation, CapEx: user-defined growth rates
* Working Capital: modeled as a percentage of sales

The DCF spans:

* Explicit multi-year forecast
* Optional growth fade period
* Terminal value using Gordon Growth Model:

```
Terminal Value = FCFF_final × (1 + g) / (WACC - g)
```

### 2. WACC Calculation and Internal Consistency

The Weighted Average Cost of Capital (WACC) is derived from component cost formulas:

* **Cost of Equity (CAPM)**:

  ```
  Cost of Equity = Risk-Free Rate + Beta × Equity Risk Premium
  ```

* **Cost of Debt**:
  Estimated via credit spreads from interest coverage ratios using Damodaran’s table.

* **WACC Formula**:

  ```
  WACC = (E/V) × Cost of Equity + (D/V) × Cost of Debt × (1 - Tax Rate)
  ```

**Data Sources:**

* Risk-Free Rate: FRED (10Y Treasury)
* ERP: Damodaran’s ERP dataset by country
* Beta: Yahoo Finance / FMP APIs

### `beta_for_target_wacc`

This function computes the beta needed to generate a target WACC using binary search. It ensures:

* Internally consistent cost of equity
* Realistic risk modeling
* Transparent link between beta, ERP, and WACC in sensitivity analysis

### 3. Sensitivity Analysis and Graphs

#### Heatmap (2D)

Displays how the share price varies across a grid of WACC and terminal growth rate combinations. Highlights:

* Base case cell
* WACC/g inflection sensitivity
* Annotated values for interpretation

#### 3D Surface Plot

Provides a 3D visualization of the same surface, revealing curvature and steepness across the valuation landscape.

### 4. Monte Carlo Simulation

Runs 1,000 valuation scenarios by sampling uncertain inputs and recalculating the DCF and WACC in each trial.

#### Key Input Distributions:

| Variable             | Distribution | Parameters                          | Rationale                               |
| -------------------- | ------------ | ----------------------------------- | --------------------------------------- |
| Beta (β)             | Normal       | μ = model.β, σ = wacc\_sigma / ERP  | Reflects systematic risk variability    |
| Perpetual Growth (g) | Triangular   | min = 1.0%, mode = 2.0%, max = 3.0% | Reflects bounded long-term GDP growth   |
| Earnings Growth      | Triangular   | min = 3.0%, mode = 5.0%, max = 7.0% | Captures upside/downside in EBIT growth |
| CapEx Growth         | Triangular   | min = 1.0%, mode = 3.0%, max = 5.0% | Models variability in asset investment  |
| WC Efficiency Factor | Normal       | μ = 0.7, σ = 0.1                    | Represents operational adjustment range |
| WACC                 | Derived      | From sampled beta, ERP, and Rf      | Internally consistent per iteration     |

#### Output

* Histogram of simulated share prices
* Markers for median, 5th percentile, 95th percentile
* Optional export of results

---

## Notebooks Included

| Notebook File                                   | Description                                      |
| ----------------------------------------------- | ------------------------------------------------ |
| `Automated_DCF_and_sensitivity_analysis.ipynb`  | Core engine: valuation, graphs, simulation       |
| `how to extract financial modelling prep.ipynb` | Tutorial on retrieving FMP data                  |
| `EDA DCF.ipynb`                                 | Exploration of raw data and company fundamentals |
| `fred_BondYields_extraction.ipynb`              | Pull US 10Y Treasury data from FRED              |
| `ERP_damodaran.ipynb`                           | Download and parse Damodaran ERP Excel dataset   |

---

## Data Freshness Overview

Each time you run the code, the following datasets are pulled live or near-live from their sources:

| Data Type                     | Source                            | Freshness at Runtime               | Notes                                                 |
| ----------------------------- | --------------------------------- | ---------------------------------- | ----------------------------------------------------- |
| Financial Statements          | Financial Modeling Prep (FMP) API | Real-time or last reported         | Quarterly/annual reports depending on company filings |
| Beta                          | Yahoo Finance / FMP               | Live                               | Queried fresh per ticker                              |
| Risk-Free Rate (10Y Treasury) | FRED (Federal Reserve)            | Updated daily                      | Reflects current US yield curve                       |
| Equity Risk Premium (ERP)     | Damodaran’s NYU ERP Excel         | Manual refresh (monthly/quarterly) | Based on latest published values                      |
| Consensus Target Prices       | Yahoo Finance / FMP               | Live                               | For comparison to implied valuation                   |
| Bond Yields (Cost of Debt)    | FRED / calculated                 | Daily or on-demand                 | Used for credit spread & interest coverage estimation |

---

## Requirements

```bash
pip install pandas numpy matplotlib seaborn requests scipy yfinance openpyxl
```

---

## How to Use

1. Clone the repository
2. Open `Automated_DCF_and_sensitivity_analysis.ipynb`
3. Set your FMP and FRED API keys
4. Run all cells to:

   * Retrieve real-time data
   * Forecast FCFF
   * Compute enterprise & equity value
   * Analyze sensitivity
   * Run Monte Carlo simulations

---

## Example Company: Apple Inc. (AAPL)

The core notebook includes a complete DCF analysis for AAPL:

* API-based financials
* WACC and cost of equity calculation
* Valuation under different growth scenarios
* Comparison to market and analyst targets

---

## License

MIT License — open for use, adaptation, and extension with attribution.

---

Let me know if you'd like this exported into a file, integrated into the notebook as a markdown cell, or used to generate a GitHub landing page.
