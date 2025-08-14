# Retail Product Placement — Sales Volume Prediction (Supermarket)

> Predicting sales volume from supermarket product placement (brochures vs. in‑store displays) using feature engineering + Linear Regression + Random Forest. Best model (RF) explains ~84% of variance; price, day (seasonality), product category, and store size are the strongest drivers. Mailers and displays have modest effects individually, but the **combination** helps.


---

## Why this project?
Retailers in a high‑inflation, competitive market need to understand what actually drives unit sales. Here I evaluate whether **product placement** (in brochures and in‑store displays) predicts sales volume and how effects vary across product categories. The work combines **EDA**, **feature engineering**, and **benchmarked models** with an emphasis on business interpretability and reproducibility.

This repository contains:
- 📓 `Retail Product Placement — Sales Volume Prediction.ipynb` — the full, reproducible analysis
- 📄 `Retail Product Placement — Sales Volume Prediction.pdf` — the write‑up with methodology and results
- ⚙️ Environment files (`requirements.txt`, `environment.yml`) and `.gitignore`

> **Note on data**: The original course dataset is not redistributed here. Place your local CSVs in `data/raw/` and point the notebook to them (see **Quickstart**).

---

## Data (schema)
We use three datasets and join on **PRODUCT_ID**, **WEEK_NO**, **STORE_ID**:
1) `transaction_data.csv` — household transactions (day, quantity, sales, discounts, etc.).  
2) `product.csv` — product hierarchy: department, commodity, sub‑commodity, brand.  
3) `causal_data.csv` — placement: **DISPLAY** and **MAILER** coding.

**Placement coding (examples):**
- **DISPLAY**: `1=Store Front`, `2=Store Rear`, `3=Front End Cap`, `4=Mid‑Aisle End Cap`, `5=Rear End Cap`, `7=In‑Aisle`, `9=Secondary Location`, `A=In‑Shelf`.
- **MAILER**: `A=Interior page feature`, `C=Interior page line item`, `D=Front page feature`, `F=Back page feature`, `H=Wrap front feature`, `J=Wrap interior coupon`, `L=Wrap back feature`, `P=Interior page coupon`, `X=Free interior`, `Z=Free front/back/wrap`.

> Derived features: `COUPON_FLAG` (from coupon discounts), `INTERACTION` (DISPLAY × MAILER), `PRODUCT_PRICE` (backed out from sales/discounts), and **store size clusters** via K‑means.

---

## Methods
- **EDA**: seasonality & peak hours; conversion from placement (mailer/display/interaction) to realised transactions; category‑level preferences.
- **Modelling**
  - **Multiple Linear Regression (OLS)** for interpretability (with standardisation).
  - **Random Forest Regressor** (no scaling required) with **time‑ordered split** and **GridSearchCV** (n_estimators, max_depth, min_samples_split, min_samples_leaf).
- **Metrics**: R² and RMSE; out‑of‑sample validation with time‑series split.

---

## Results (high‑level)
- **Random Forest** achieves **R² ≈ 0.84** out‑of‑sample with tuned hyperparameters.
- **Top drivers**: **price**, **day (seasonality)**, **department**, **store‑size cluster**, **sub‑commodity**.
- **Placement**: mailer and display are individually weaker but their **interaction** helps; brochures (some types) often dominate displays; effect varies by department (e.g., drug‑related items, packaged meats).

**Business takeaways**
- Lead with **pricing & timing**; match **placement** to category behaviour.  
- Leverage **combined** brochure + in‑store campaigns for uplift; tailor to store size and top categories.

> See the notebook for code and the PDF for figures and deeper analysis.

---

## Reproducibility notes
- **Time‑aware splits** preserve temporal ordering (train on earlier weeks, evaluate on later weeks).  
- **Scaling** is applied only where required (e.g., OLS). Tree‑based models use raw features.  
- **Hyperparameters** are grid‑searched (documented in the notebook).  
- **Random seeds** set where applicable for repeatability.

---

## Limitations & next steps
- Data coverage and missingness in placement fields can affect estimates.  
- Mixed units (e.g., fuel in litres vs. discrete items) challenge RMSE comparability.  
- **Next**: add calendar/holiday effects, competitor signals, store geography, and uplift modelling; move pipeline to `src/` with tests and CI.

---

## Tech stack
Python (pandas, NumPy, seaborn/matplotlib, scikit‑learn, statsmodels), Jupyter.

---

## License
This project is released under the MIT License (see `LICENSE`).

