# ShopFlow Data Dictionary

## 1. Dataset Overview

**Dataset:** Olist Brazilian E-Commerce Public Dataset

**Business domain:** E-commerce

**Purpose:**  
This data dictionary documents the structure, meaning, grain, identifiers,
relationships, and analytical role of the source tables used in the
ShopFlow E-Commerce Analytics Platform.

The source dataset contains information about customers, orders, order
items, products, sellers, payments, reviews, product categories, and
geographic locations.

---

# 2. Customers

**Source file:** `olist_customers_dataset.csv`

**Grain:** One customer-ordering record.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `customer_id` | String | Identifier assigned to a customer record associated with an order. It uniquely identifies the row in this source table. | Primary Key |
| `customer_unique_id` | String | Identifier representing the underlying unique customer across customer records. Multiple `customer_id` values can belong to the same unique customer. | Business Identifier |
| `customer_zip_code_prefix` | Integer | First five digits/prefix of the customer's Brazilian postal code. | Attribute |
| `customer_city` | String | City associated with the customer's address. | Attribute |
| `customer_state` | String | Brazilian state associated with the customer's address. | Attribute |

### Key observations

- `customer_id` is unique within the source table.
- `customer_unique_id` is not unique within the source table.
- `customer_unique_id` is therefore more appropriate for unique-customer analysis.
- `customer_id` is used to connect customer records to the Orders table.

---

# 3. Orders

**Source file:** `olist_orders_dataset.csv`

**Grain:** One customer order.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `order_id` | String | Unique identifier of an e-commerce order. | Primary Key |
| `customer_id` | String | Identifier of the customer record associated with the order. | Foreign Key |
| `order_status` | String | Current lifecycle status of the order, such as delivered, shipped, canceled, or processing. | Categorical Attribute |
| `order_purchase_timestamp` | DateTime | Timestamp when the customer placed the order. | Date/Time |
| `order_approved_at` | DateTime | Timestamp when payment for the order was approved. | Date/Time |
| `order_delivered_carrier_date` | DateTime | Date when the order was handed over to the logistics carrier. | Date/Time |
| `order_delivered_customer_date` | DateTime | Date when the order was delivered to the customer. | Date/Time |
| `order_estimated_delivery_date` | DateTime | Estimated delivery date provided for the order. | Date/Time |

### Key observations

- `order_id` identifies an order.
- `customer_id` connects an order to the Customers table.
- An order can contain multiple order items.
- The order timestamps allow delivery-performance and order-lifecycle analysis.

---

# 4. Order Items

**Source file:** `olist_order_items_dataset.csv`

**Grain:** One product line/item within an order.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `order_id` | String | Identifier of the order containing the item. | Foreign Key |
| `order_item_id` | Integer | Sequential item number identifying a particular item line within an order. | Composite Key Component |
| `product_id` | String | Identifier of the product purchased in the item line. | Foreign Key |
| `seller_id` | String | Identifier of the seller responsible for the item. | Foreign Key |
| `shipping_limit_date` | DateTime | Deadline by which the seller is expected to ship the item. | Date/Time |
| `price` | Numeric | Price charged for the individual product item, excluding freight. | Measure |
| `freight_value` | Numeric | Freight/shipping charge associated with the individual item. | Measure |

### Key observations

- The grain is finer than the Orders table.
- One order can contain multiple order-item rows.
- `order_id` alone is not sufficient to uniquely identify an item row.
- `order_id + order_item_id` identifies an item line within an order.
- `price` and `freight_value` are important measures for revenue and shipping analysis.

---

# 5. Products

**Source file:** `olist_products_dataset.csv`

**Grain:** One product.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `product_id` | String | Unique identifier of a product. | Primary Key |
| `product_category_name` | String | Product category name in the source dataset's original language. | Attribute / Reference Key |
| `product_name_lenght` | Numeric | Number of characters in the product name. | Attribute |
| `product_description_lenght` | Numeric | Number of characters in the product description. | Attribute |
| `product_photos_qty` | Numeric | Number of photographs associated with the product listing. | Attribute |
| `product_weight_g` | Numeric | Product weight in grams. | Physical Attribute |
| `product_length_cm` | Numeric | Product length in centimeters. | Physical Attribute |
| `product_height_cm` | Numeric | Product height in centimeters. | Physical Attribute |
| `product_width_cm` | Numeric | Product width in centimeters. | Physical Attribute |

### Key observations

- `product_id` uniquely identifies a product.
- Product information provides descriptive context for order-item transactions.
- `product_category_name` can be mapped to an English category using the category translation table.
- Physical attributes can be used for product and logistics analysis.

> Note: The source dataset uses the spelling `lenght` in these column names. The original source spelling is retained in this dictionary.

---

# 6. Sellers

**Source file:** `olist_sellers_dataset.csv`

**Grain:** One seller.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `seller_id` | String | Unique identifier of a seller registered on the marketplace. | Primary Key |
| `seller_zip_code_prefix` | Integer | First five digits/prefix of the seller's Brazilian postal code. | Attribute |
| `seller_city` | String | City associated with the seller's address. | Attribute |
| `seller_state` | String | Brazilian state associated with the seller's address. | Attribute |

### Key observations

- `seller_id` uniquely identifies a seller.
- Seller information provides geographic context for order-item transactions.
- Seller performance can be analyzed using order-item, delivery, freight, and review information.

---

# 7. Order Payments

**Source file:** `olist_order_payments_dataset.csv`

**Grain:** One payment record associated with an order.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `order_id` | String | Identifier of the order associated with the payment record. | Foreign Key |
| `payment_sequential` | Integer | Sequential number identifying a payment record within an order. | Composite Key Component |
| `payment_type` | String | Payment method used for the payment, such as credit card, boleto, voucher, or debit card. | Categorical Attribute |
| `payment_installments` | Integer | Number of installments selected for the payment. | Measure / Attribute |
| `payment_value` | Numeric | Monetary value represented by the payment record. | Measure |

### Key observations

- One order can have multiple payment records.
- `order_id + payment_sequential` identifies a payment record.
- Payment data can be used to analyze payment-method usage, installment behavior, and monetary value.

---

# 8. Order Reviews

**Source file:** `olist_order_reviews_dataset.csv`

**Grain:** One customer review record associated with an order.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `review_id` | String | Identifier of the review record. | Primary Key Candidate |
| `order_id` | String | Identifier of the order associated with the review. | Foreign Key |
| `review_score` | Integer | Customer rating assigned to the order, on a scale from 1 to 5. | Measure |
| `review_comment_title` | String | Optional title written by the customer as part of the review. | Text Attribute |
| `review_comment_message` | String | Optional written review message provided by the customer. | Text Attribute |
| `review_creation_date` | DateTime | Date when the review was created. | Date/Time |
| `review_answer_timestamp` | DateTime | Timestamp associated with the review response/answer. | Date/Time |

### Key observations

- `review_score` provides a direct measure of customer satisfaction.
- Review text fields can be missing even when a valid review score exists.
- Reviews can be connected to operational data through `order_id`.
- Review data can support customer-experience and delivery-performance analysis.

---

# 9. Product Category Translation

**Source file:** `product_category_name_translation.csv`

**Grain:** One product-category translation.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `product_category_name` | String | Original product category name used by the Brazilian source dataset. | Primary/Reference Key |
| `product_category_name_english` | String | English translation of the product category name. | Attribute |

### Key observations

- The table provides a mapping between the original category name and its English equivalent.
- It can be joined to Products using `product_category_name`.
- It is reference/lookup data rather than transactional data.

---

# 10. Geolocation

**Source file:** `olist_geolocation_dataset.csv`

**Grain:** One geographic coordinate record associated with a postal-code prefix/location combination.

### Columns

| Column | Data Type | Description | Role |
|---|---|---|---|
| `geolocation_zip_code_prefix` | Integer | Brazilian postal-code prefix associated with the geographic record. | Reference Key |
| `geolocation_lat` | Numeric | Latitude of the geographic location. | Geographic Attribute |
| `geolocation_lng` | Numeric | Longitude of the geographic location. | Geographic Attribute |
| `geolocation_city` | String | City associated with the geographic record. | Geographic Attribute |
| `geolocation_state` | String | Brazilian state associated with the geographic record. | Geographic Attribute |

### Key observations

- The table provides geographic information associated with postal-code prefixes.
- It can support geographic analysis of customers and sellers.
- A postal-code prefix may have multiple geographic records, so the table should not automatically be treated as a one-row-per-postal-code dimension without validation.

---

# 11. Source Relationship Summary

```text
CUSTOMERS
    |
    | customer_id
    |
    v
ORDERS
    |
    +--------------------+
    |                    |
    | order_id           | order_id
    v                    v
ORDER_ITEMS          PAYMENTS
    |
    +--------------------+
    |                    |
    | product_id         | seller_id
    v                    v
PRODUCTS              SELLERS
    |
    | product_category_name
    v
CATEGORY TRANSLATION

ORDERS
    |
    | order_id
    v
REVIEWS