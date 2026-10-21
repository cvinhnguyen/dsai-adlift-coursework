# AdLift Marketing — Data Dictionary

---

## users.csv
One row per user. This is the primary table.

| Column | Type | Description |
|---|---|---|
| `user_id` | string | Unique user identifier (e.g. `U00000001`) |
| `age_group` | string | Age band: `18-24`, `25-34`, `35-44`, `45-54`, `55+` |
| `gender` | string | `M`, `F`, or `X` (non-binary/unspecified) |
| `top_interest` | string | User's primary product interest category |
| `is_loyalty_member` | int | 1 if the user is enrolled in the loyalty programme |
| `device_preference` | string | Preferred device: `mobile`, `desktop`, `tablet` |
| `days_since_last_visit` | float | Days since the user last visited the site (some missing) |
| `n_past_purchases` | int | Number of purchases made in the previous 12 months |

---

## ad_exposures.csv
One row per ad impression. Contains duplicate rows (ad-server logging errors).

| Column | Type | Description |
|---|---|---|
| `exposure_id` | string | Unique impression identifier |
| `user_id` | string | User who saw the ad |
| `campaign_id` | string | Campaign that served the ad |
| `exposure_date` | date | Date the ad was shown |
| `channel` | string | Ad channel: `social_media`, `search`, `display`, `email`, `video` |
| `creative_type` | string | Ad format: `carousel`, `text_ad`, `banner`, `pre_roll`, `newsletter`, `story_ad`, `dynamic`, `in_stream` |
| `ad_position` | int | Position of the ad (1 = most prominent; some missing) |
| `was_clicked` | int | 1 if the user clicked the ad |

---

## conversions.csv
One row per conversion (purchase) event.

| Column | Type | Description |
|---|---|---|
| `conversion_id` | string | Unique conversion identifier |
| `user_id` | string | User who converted |
| `conversion_date` | date | Date of the purchase |
| `order_value` | float | Value of the order (£; some missing) |
| `last_campaign_id` | string | Last campaign the user was exposed to before converting |

---

## campaigns.csv
One row per campaign.

| Column | Type | Description |
|---|---|---|
| `campaign_id` | string | Unique campaign identifier |
| `campaign_name` | string | Campaign name |
| `channel` | string | Delivery channel |
| `creative_type` | string | Ad format |
| `budget_gbp` | int | Total campaign budget (£) |
| `base_cvr` | float | Historical baseline conversion rate for this campaign |
| `start_date` | date | Campaign start date |
| `end_date` | date | Campaign end date |

---

## Notes for Students

- **Deduplication required**: `ad_exposures.csv` contains duplicate rows due to ad-server logging errors. Deduplicate on `exposure_id` before aggregating.
- **Create a binary target**: `users.csv` does not contain a `converted` column. You must create it by checking whether each `user_id` appears in `conversions.csv`.
- **Feature aggregation**: The model needs per-user features. Aggregate `ad_exposures.csv` by `user_id` to create: total exposures, total clicks, click rate, channel breakdown, etc.
- **Users with no exposures**: some users were never served an ad — handle missing values for all exposure features.
- **Campaign join**: use `campaigns.csv` to understand the quality of each campaign (e.g. `base_cvr`). You can aggregate the best campaign CVR a user was exposed to.
