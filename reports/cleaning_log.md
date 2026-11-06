# Data Cleaning Log

## Source
Brazilian E-Commerce Public Dataset by Olist (Kaggle)
Period: 2016-09 to 2018-10

## Decisions

### 1. Date columns
- All 5 timestamp columns converted with `pd.to_datetime(errors="coerce")`.
- Invalid date strings → NaT (none found / N found: **X**).

### 2. Missing values
| Column | Nulls | Decision | Reason |
|--------|-------|----------|--------|
| order_approved_at | 160 (0.16%) | Keep | Mostly canceled/unavailable orders |
| order_delivered_carrier_date | 1,783 (1.79%) | Keep | Not yet shipped |
| order_delivered_customer_date | 2,965 (2.98%) | Keep | Not yet delivered; filtered by status instead |
| product_category_name | 610 (1.85%) | Fill "unknown" | Preserve revenue; label missing category |
| review_comment_* | >50% | Keep | Optional fields; not used in metrics |

### 3. Filters applied
- **`order_master` uses only `order_status = 'delivered'`** for revenue analysis.
  Rationale: canceled/unavailable orders did not generate realized revenue.

### 4. Aggregations
- Payments aggregated to one row per `order_id`.
- Reviews deduplicated to one row per `order_id` (kept latest by `review_answer_timestamp`).
- Products merged with English category translation.

### 5. Row count summary
| Stage | Rows |
|-------|------|
| Raw items | 112,650 |
| Items in delivered orders | **X** |
| After all joins | **X** |

### 6. Known limitations
- Repeat-buyer rate is low (~3%) because the dataset models "one customer record per order."
- Revenue figures are in BRL, nominal (not adjusted for inflation).
- No customer demographics → segmentation limited to RFM + geography.
- Reviews have a small number of duplicate review_ids (see Day 2).