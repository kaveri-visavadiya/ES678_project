# ES678 Project - 'Online Retail: Can early purchase behavior identify customers who will return?'

**Research question:** Can a customer's purchasing behavior before a fixed prediction date predict the number of distinct purchase invoices they place during the following 90 days?

This project uses the UCI Online Retail transaction dataset to construct one customer-level observation per eligible customer and compare simple baselines with Poisson regression and random-forest regression.

## Repository structure

```text
online-retail-purchase-frequency/
├── README.md
├── paper/
│   └── ES678_proj.pdf
├── notebook/
│   └── ES678_proj.ipynb
├── data/
    └── Online Retail.xlsx       # downloaded locally; do not commit by default
```

## About the Dataset

- **Source:** [UCI Machine Learning Repository — Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail)
- **Dataset DOI:** [10.24432/C5BW33](https://doi.org/10.24432/C5BW33)
- **Original size:** 541,909 invoice-line rows and 8 columns.
- **Period:** 1 December 2010 to 9 December 2011.
- **License:** UCI lists the dataset under Creative Commons Attribution 4.0 (CC BY 4.0). 

## How to run the notebook? Reproducibility

Use Python with Jupyter Notebook or JupyterLab.

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn scikit-learn openpyxl jupyter
jupyter notebook
```

Open the notebook and run all cells in order. The notebook's first setup cell also contains a `%pip install` command; it may be left commented out after installing the dependencies in the environment.

The notebook downloads the source data from:

```text
https://archive.ics.uci.edu/static/public/352/online+retail.zip
```

The source data is not included in this repository. The notebook downloads the UCI ZIP archive automatically and extracts `Online Retail.xlsx` into `data/`. Alternatively, download the .xlsx file from the UCI dataset page and place it at `data/Online Retail.xlsx`.

## About the project 
### Prediction task

- **Unit:** one customer (`CustomerID`).
- **Prediction cutoff:** 1 September 2011.
- **Observation window:** 1 December 2010 to 31 August 2011 (9 months).
- **Target:** number of distinct completed invoice numbers in the next 3 months (1 Sep 2011 to 1 Dec 2011).
- **Eligible population:** customers with at least one completed invoice during the observation window.

The target is a **count**; a customer with no completed invoice in the outcome window receives a target of zero. 

An invoice can contain multiple product lines. The target counts distinct invoice numbers, not transaction rows or product lines.

### Cleaning and feature construction

The notebook:
1. Removes rows without a customer ID or valid invoice date.
2. Excludes cancellation invoices (invoice numbers beginning with `C`).
3. Excludes non-positive quantities and non-positive unit prices.
4. Removes exact duplicate rows.
5. Aggregates invoice lines to customer-level behavioural features using only the observation window.
6. Counts distinct invoice numbers in the outcome window to create the target.

### Models

The notebook compares:
- Training-set mean baseline (`DummyRegressor`).
- Persistence baseline: previous 3-month invoice count predicts the next 3-month count.
- Poisson regression with median imputation, `log1p`, standardization, and L2 regularization.
- Random forest regression with median imputation.

### Evaluation

The intended split is a fixed customer-level 60/20/20 train/validation/test split with random seed 42. Model selection is based on validation MAE. The test set is held out for final reporting. 

Primary metric: MAE in invoices. RMSE, Poisson deviance, and R-squared are supplementary metrics. Predictions are continuous expected counts; negative predictions are clipped to zero.

## Citation

Chen, D. (2015). *Online Retail* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33.

[scikit-learn — PoissonRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.PoissonRegressor.html).

[scikit-learn — RandomForestRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html).

[scikit-learn — Regression metrics](https://scikit-learn.org/stable/modules/model_evaluation.html).
