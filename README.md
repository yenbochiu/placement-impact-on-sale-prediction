# Retail Product Placement — Sales Volume Prediction (Supermarket)

> **TL;DR**: Predicting sales volume from supermarket product placement (brochures vs. in‑store displays) using feature engineering + Linear Regression + Random Forest. Best model (RF) explains ~84% of variance; price, day (seasonality), product category, and store size are the strongest drivers. Mailers and displays have modest effects individually, but the **combination** helps.

**Korte samenvatting (NL)**: Dit project voorspelt verkochte aantallen in supermarkten o.b.v. productplaatsing (folders vs. schapinrichting). Met een Random Forest‑model (R² ≈ 0.84) blijken **prijs**, **dag/seasonaliteit**, **categorie** en **winkelgrootte** de belangrijkste factoren. Folders en displays afzonderlijk hebben beperkte voorspellingskracht; gecombineerd werken ze beter.

---

## Why this project?
Retailers in a high‑inflation, competitive market need to understand what actually drives unit sales. Here I evaluate whether **product placement** (in brochures and in‑store displays) predicts sales volume and how effects vary across product categories. The work combines **EDA**, **feature engineering**, and **benchmarked models** with an emphasis on business interpretability and reproducibility.

This repository contains:
- 📓 `GROUP_13_Code.ipynb` — the full, reproducible analysis
- 📄 `Group_13_Report.pdf` — the write‑up with methodology and results
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

## Repository structure
```
.
├── GROUP_13_Code.ipynb         # Main Jupyter Notebook (analysis + modelling)
├── Group_13_Report.pdf         # Project report
├── requirements.txt            # Pip environment (Python 3.11+ recommended)
├── environment.yml             # Conda environment (optional)
├── .gitignore                  # Ignore data/outputs/OS junk
├── LICENSE                     # MIT (replace with your name)
└── data/
    └── raw/                    # Place CSVs here (not tracked)
```
Optional (for scaling up): `src/` (ETL & feature engineering), `models/`, `figures/`, `data/processed/`.

---

## Quickstart

### 1) Create environment
Using **pip**:
```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Using **conda**:
```bash
conda env create -f environment.yml
conda activate retail-placement
```

### 2) Prepare data
Create a folder and copy the CSVs:
```
data/raw/transaction_data.csv
data/raw/product.csv
data/raw/causal_data.csv
```

Open the notebook and adjust the read paths at the top (or set a `DATA_DIR` variable):
```python
DATA_DIR = "data/raw/"
transaction_data = pd.read_csv(DATA_DIR + "transaction_data.csv")
product = pd.read_csv(DATA_DIR + "product.csv")
causal_data = pd.read_csv(DATA_DIR + "causal_data.csv")
```

### 3) Run the analysis
```bash
jupyter notebook GROUP_13_Code.ipynb
```
The notebook runs EDA ➜ feature engineering ➜ modelling (OLS + RF) ➜ evaluation, and saves the tuned RF model (`random_forest_opt.joblib`).

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

## Acknowledgements
Course dataset & collaboration context as per the included report. This repository is structured and documented by **you** for public viewing as a personal portfolio sample.

---

### Recruiter note (The Netherlands)
Hands‑on retail analytics with clear business framing, clean notebook, and reproducible environment. Comfortable with scikit‑learn timeseries splits, feature engineering (pricing, placement, clustering), model tuning, and communicating impact.
