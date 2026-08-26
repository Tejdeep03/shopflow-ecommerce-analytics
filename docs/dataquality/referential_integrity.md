# Sprint 2.5 — Referential Integrity

## Objective

Verify that identifiers used to connect tables reference valid records in the corresponding parent or lookup table.

The process followed was:

**Identify relationship → Identify parent key → Test references → Count valid/invalid references → Investigate failures → Document findings**

---

## Relationship Inventory

The dataset contains the following cross-table relationships:

1. `orders.customer_id → customers.customer_id`
2. `order_items.order_id → orders.order_id`
3. `order_items.product_id → products.product_id`
4. `order_items.seller_id → sellers.seller_id`
5. `payments.order_id → orders.order_id`
6. `reviews.order_id → orders.order_id`
7. `products.product_category_name → category_translation.product_category_name`

No other cross-table key relationships were identified from the available schema.

---

## Results

| Relationship | Valid References | Broken References | Status |
|---|---:|---:|---|
| orders → customers | 99,441 | 0 | PASS |
| order_items → orders | 112,650 | 0 | PASS |
| order_items → products | 112,650 | 0 | PASS |
| order_items → sellers | 112,650 | 0 | PASS |
| payments → orders | 103,886 | 0 | PASS |
| reviews → orders | 99,224 | 0 | PASS |
| products → category_translation | 32,328 valid product rows | 13 unmatched rows | ANOMALY |

---

## Category Translation Finding

The `category_translation` table contains:

- 71 rows
- 71 non-null `product_category_name` values
- 0 duplicate `product_category_name` values

The `products` table contains:

- 32,951 total products
- 32,341 products with a category
- 610 products with a missing category

Among products with non-null categories:

- 32,328 have a corresponding translation
- 13 do not have a corresponding translation

The 13 unmatched product records contain only 2 distinct category names:

- `portateis_cozinha_e_preparadores_de_alimentos` — 10 products
- `pc_gamer` — 3 products

### Classification

The 610 products with missing categories are completeness issues identified during Sprint 2.1.

The 13 products with non-null but unmatched categories are referential-integrity / lookup-coverage anomalies.

The original source values will be preserved. No translation will be invented during the data-quality assessment.

---

## Key Learning

Referential integrity asks:

> Does a value used to connect one table to another actually exist in the referenced table?

Repeated foreign-key values are not necessarily problems. For example, one order can have multiple order items and multiple payment records.

The important check is whether the referenced parent/lookup key exists.

In Pandas, `.isin()` was used to test whether values from a child table exist in the corresponding parent or lookup table.

Example:

```python
order_items["order_id"].isin(orders["order_id"])
