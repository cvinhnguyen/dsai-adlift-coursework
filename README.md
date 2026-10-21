# AdLift: Ad Conversion Prediction

DSAI mini coursework (FSDS K11). AdLift is a UK digital marketing agency that can only afford to target 20% of its users with paid ads. This project scores each user by how likely they are to make a purchase, so the budget goes to the users most likely to convert.

## Results

| Model | CV AUC (5-fold) | Validation AUC | Train/val gap |
|---|---|---|---|
| Logistic Regression (chosen) | 0.884 | 0.871 | 0.019 |
| Random Forest (tuned) | 0.884 | 0.868 | 0.060 |
| Random Forest (default) | 0.872 | 0.851 | 0.149 |

Both models score the same, so I chose logistic regression because it overfits less, gives realistic probabilities and is easier to explain.

On the validation set (1,600 users, 236 buyers), the model's top 20% contains 72% of all buyers, against 20% for random targeting. That is 3.6 times more buyers for the same budget, and net revenue of £19,950 compared with £4,846 (assuming £120 per conversion and £3 per wasted target). Targeting just the top 3.4% of users already reaches as many buyers as random targeting with 20%.

The strongest signals are loyalty membership, the number of past purchases and clicks on ads.

## Files

| File | What it is |
|---|---|
| `coursework_solution.ipynb` | My full solution with outputs: EDA, feature engineering, models, evaluation, business impact, monitoring, SHAP and attribution |
| `coursework.ipynb` | The original coursework template |
| `model.pkl` | The chosen model (scikit-learn pipeline: median imputer, scaler, logistic regression), saved with `joblib` |
| `business_brief.md` | The business problem and success criteria |
| `data_dictionary.md` | Column descriptions for the four tables |
| `data/` | `users.csv`, `ad_exposures.csv`, `conversions.csv`, `campaigns.csv` |

## Things I found in the data

- The brief says to remove duplicate impressions using `exposure_id`, but every id is unique. 588 rows copy another row in every other column, so the duplicates were given new ids. I removed duplicates using all columns except the id.
- 419 users never saw an ad, and 15 of them still bought something. The model predicts who is likely to buy, which is not the same as who buys because of an ad. A control group would be needed to measure the real effect of the ads.
- Ads are only logged from January to 1 May 2025, and 82% of them are dated on the last day of their campaign. This makes first-click and last-click attribution reflect when each campaign ended more than how well it worked.
- `last_campaign_id` in `conversions.csv` matches the user's real last campaign only 45% of the time.

## How to run

I use [uv](https://docs.astral.sh/uv/) to create the virtual environment and install the packages. If you don't have uv yet, install it with `pip install uv` or follow the [uv installation guide](https://docs.astral.sh/uv/getting-started/installation/).

Run these commands from this folder:

```bash
# 1. Create a virtual environment in .venv (uv downloads Python 3.13 if it is not installed)
uv venv --python 3.13

# 2. Activate it
#    Windows (PowerShell or cmd):  .venv\Scripts\activate
#    Windows (Git Bash):           source .venv/Scripts/activate
#    macOS / Linux:                source .venv/bin/activate

# 3. Install the packages
uv pip install pandas numpy scikit-learn matplotlib seaborn shap joblib jupyter

# 4. Open the notebook
jupyter notebook coursework_solution.ipynb
```

The notebook was tested in a fresh environment created this way, with Python 3.13, pandas 3.0, numpy 2.5, scikit-learn 1.9, matplotlib 3.11, seaborn 0.13 and shap 0.54.

The notebook reads the CSV files from `data/`, so start Jupyter from this folder. A full run takes about two minutes, mostly for the random forest search, and it overwrites `model.pkl`. The `.venv` folder is listed in `.gitignore`, so it will not be committed.

To use the saved model, build the same features as in Section 4 of the notebook, then load it as below. A `joblib` file can run code when it is loaded, so only load a `model.pkl` you trust, such as one you produced by running the notebook yourself.

```python
import joblib

model = joblib.load('model.pkl')
probabilities = model.predict_proba(X[model.feature_names_in_])[:, 1]
```

## Author

Vinh Nguyen
