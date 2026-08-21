# Commerce Lakehouse Pipeline

## Overview

This project builds a Databricks Lakehouse pipeline from five Commerce CSV exports: customers, products, orders, order items and payments.

The pipeline uses Bronze, Silver and Gold layers. Invalid records are not dropped silently; they are written to quarantine with the reason they failed validation.

## Notebooks

Run them in this order:

1. `01_data_exploration`
2. `02_data_cleaning`
3. `03_business_analysis`

Notebook 1 loads the CSV files with explicit schemas, profiles the data and writes Bronze Delta tables.

Notebook 2 cleans and validates the Bronze data, checks relationships, removes duplicates, writes trusted Silver tables and saves rejected records to quarantine.

Notebook 3 builds Finance reconciliation, trusted revenue outputs, reporting datasets, the data-quality summary and an `explain()` example.

## Bronze

Bronze keeps the source data close to the original files and adds only traceability information.

`order_date` and `payment_date` are intentionally stored as strings in Bronze. Date parsing happens in Silver so malformed values can be detected instead of being lost during ingestion.

Bronze tables:

- `bronze_customers`
- `bronze_products`
- `bronze_orders`
- `bronze_order_items`
- `bronze_payments`

The original CSV files are never edited.

## Silver

Silver contains the trusted business entities used by Gold.

Main checks include:

- valid identifiers and required fields
- duplicate business IDs
- valid dates
- valid quantities, prices and payment amounts
- allowed order and payment statuses
- customer references used by orders
- order and product references used by order items
- order references used by payments

Rejected records are saved in `quarantine_records` together with the validation reason and original source values.

Silver tables:

- `silver_customers`
- `silver_products`
- `silver_orders`
- `silver_order_items`
- `silver_payments`

## Main Business Rules

Customer and product IDs must be positive. Orders must have a valid customer and valid order date. Order items must have a positive quantity and unit price and must reference trusted orders and products.

Payment status and amount must agree. Paid payments must be positive, failed payments must be zero, and refunds must be negative.

A payment dated before its order is treated as invalid because the brief does not define prepayments as a supported business process. If the real system allows prepayments, this rule should be changed.

Minor descriptive issues are standardized when they do not make the business entity unusable. For example, customer type, city, category and payment method formatting are normalized. Missing customer names become `Unknown`, and malformed emails become null.

## Finance Reconciliation

Expected order value is calculated from trusted order items:

`quantity * unit_price`

Those line values are summed per order and compared with usable payment activity.

Each order is classified as one of these conditions:

- `MATCHED`
- `UNDERPAID`
- `OVERPAID`
- `MISSING_PAYMENT`
- `REFUNDED`
- `NON_STANDARD`

Trusted revenue includes only completed orders whose expected order value matches trusted payment activity.

Gold outputs make it possible to analyze trusted revenue by date, city, product, category, customer and customer type, and to investigate reconciliation differences.

## Duplicate Handling

Exact duplicate rows do not increase trusted counts. One copy is kept and extra copies are quarantined.

If the same business ID has different versions after exact duplicates are removed, those records are treated as conflicting identifiers and quarantined rather than choosing one silently.

## Spark `explain()` and Lazy Evaluation

Notebook 3 calls `explain()` on a meaningful transformation that joins trusted revenue orders with order items and products before aggregation.

Spark does not execute the full transformation when the DataFrame is defined. It builds and optimizes a plan first. Execution starts when an action such as `display()`, `count()` or a Delta write is called. This is Spark lazy evaluation.

## If the Data Became Very Large

For much larger datasets, I would keep the same business rules but improve execution by using incremental ingestion, Delta `MERGE`, fewer repeated full-table actions, appropriate join strategies, table statistics, file compaction and clustering or partitioning based on real query patterns.

I would also add orchestration, monitoring and data-quality alerts instead of relying only on notebook execution.
