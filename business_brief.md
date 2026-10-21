# AdLift — Ad Conversion Prediction
## Mini Coursework Business Brief

---

### Company Background

**AdLift** is a UK digital marketing agency that manages paid advertising campaigns for e-commerce clients across social media, search, display, email, and video channels. AdLift manages £8M+ in annual ad spend and is under pressure from clients to demonstrate measurable ROI on every campaign.

The Analytics team is building a **conversion prediction model** to help AdLift's campaign managers decide which users to target — and at what spend level — in order to maximise conversions within a fixed budget.

---

### The Business Problem

AdLift currently targets users based on simple demographic segments (e.g. "females aged 25–34 who liked a product page"). This approach ignores individual purchase history, ad engagement behaviour, and campaign-specific conversion rates.

The team wants a model that scores each user and predicts whether they will convert (make a purchase) in the next 30 days, given their profile and ad exposure history. The top 20% of highest-scoring users will receive targeted ad spend; the rest will not be actively targeted.

---

### Your Task

You have been given access to AdLift's user database and campaign records. The data covers the period **2025-01-01 to 2026-06-20**. A user **converts** if they appear in `conversions.csv`.

Your task is to:

1. Explore and understand the data
2. Build a per-user feature set from `users.csv`, `ad_exposures.csv`, and `campaigns.csv`
3. Train a model that predicts conversion (`converted = 1`)
4. Evaluate the model's business value at the 20% targeting budget

---

### Data Available

| File | Description |
|---|---|
| `users.csv` | One row per user — demographics, loyalty status, browsing behaviour |
| `ad_exposures.csv` | One row per ad impression — campaign, channel, click status |
| `conversions.csv` | One row per conversion — which users actually purchased |
| `campaigns.csv` | One row per campaign — channel, creative type, budget, conversion rate |

---

### Success Criteria

- **Primary metric**: AUC-ROC. Above **0.75** is usable; above **0.83** is strong.
- **Business constraint**: Target the top **20%** highest-risk users. The **conversion capture rate** measures what fraction of all actual conversions are in your top 20% — higher is better.
- **Deployment**: Save your best model as `model.pkl` using `joblib`.

---

### Key Notes

- **Most users never convert**: only ~15% convert in any given month. Accuracy is misleading — a model that predicts 'never converts' scores 85% accuracy.
- **Click-through is a strong signal**: users who clicked an ad are far more likely to convert. Aggregate click behaviour per user from `ad_exposures.csv`.
- **Campaign channel matters**: email and search campaigns have higher conversion rates than display. Consider how many exposures a user had per channel.
- **Duplicate impressions**: ad-server logs sometimes record the same impression twice — deduplicate before aggregating.
- **Loyal customers and returning buyers** have substantially higher conversion rates — these user-level features from `users.csv` are powerful.
