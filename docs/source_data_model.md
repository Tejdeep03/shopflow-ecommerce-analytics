# ShopFlow Source Data Model

## Dataset

Olist Brazilian E-Commerce Public Dataset

## Purpose

Document the structure, grain, keys and relationships of the source
e-commerce data before transformation and analytical modelling.

---

## Source Tables

| Table | Grain | Primary Key |
|---|---|---|
| Customers | One customer record | customer_id |
| Orders | One order | order_id |
| Order Items | One item within an order | order_id + order_item_id |
| Products | One product | product_id |
| Sellers | One seller | seller_id |
| Payments | One payment record | order_id + payment_sequential |
| Reviews | One review | review_id |
| Category Translation | One category translation | product_category_name |

---

## Core Relationships

Customers → Orders

- One customer can have many orders.
- `orders.customer_id` references `customers.customer_id`.

Orders → Order Items

- One order can contain multiple order items.
- `order_items.order_id` references `orders.order_id`.

Products → Order Items

- One product can appear in multiple order items.
- `order_items.product_id` references `products.product_id`.

Sellers → Order Items

- One seller can be associated with multiple order items.
- `order_items.seller_id` references `sellers.seller_id`.

Orders → Payments

- An order can have multiple payment records.
- `payments.order_id` references `orders.order_id`.

Orders → Reviews

- Reviews are associated with orders through `order_id`.

Products → Category Translation

- Product categories can be mapped to English category names.