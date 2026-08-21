# E-Commerce Analytics Data Pipeline

## Purpose

This project creates a repeatable PySpark pipeline that converts raw e-commerce order and customer exports into trusted datasets for reporting and analysis.

The work was completed in Databricks using PySpark DataFrames and Spark SQL.

The original CSV files are not manually modified.

## Source Data

The pipeline uses:

- `orders_raw.csv`
- `customers_raw.csv`

The Databricks paths are:

`/Volumes/workspace/ecommerce_dataset/ecommerce/orders_raw.csv`

`/Volumes/workspace/ecommerce_dataset/ecommerce/customers_raw.csv`

## Execution Order

Run the notebooks in this order:

1. `01_data_exploration`
2. `02_data_cleaning`
3. `03_business_analysis`

The first notebook investigates the source data.

The second notebook creates and validates the trusted datasets.

The third notebook performs the required business analysis using only the trusted data.

## Trusted Tables

Notebook 2 saves the cleaned datasets as:

`workspace.ecommerce_dataset.orders_silver`

`workspace.ecommerce_dataset.customers_silver`

Notebook 3 loads these tables instead of rebuilding Silver data directly from the raw CSV files.

## Order Cleaning Rules

An order is excluded when:

- `order_id` is missing
- `customer_id` is missing
- the order date cannot be parsed
- quantity is missing or less than or equal to zero
- unit price is missing or less than or equal to zero

Exact duplicate records are removed.

If conflicting records with the same order ID remained after exact duplicate removal, they would be excluded instead of choosing one arbitrarily.

The supplied dataset did not contain conflicting duplicate order IDs after exact duplicate removal.

## Order Standardization

Order statuses are normalized into consistent lowercase values.

Examples:

`Completed`, `COMPLETED`, `complete` → `completed`

`cancel`, `canceled`, `CANCELLED` → `cancelled`

Payment methods are standardized into values such as:

- PayPal
- Bank Transfer
- Credit Card
- Cash
- Unknown

City names are standardized so different representations of the same city are grouped together.

For example:

`Prishtina` and `Prishtinë` → `Prishtinë`

`Gjakove` and `Gjakovë` → `Gjakovë`

Missing product categories and other grouping values are represented as `Unknown`.

## Total Amount

Each trusted order contains:

`total_amount = quantity × unit_price`

The pipeline validates that this calculated value is correct.

## Customer Cleaning Rules

Customers are matched to orders using `customer_id`.

A customer is not removed only because the name or email is missing or malformed.

Instead:

- missing customer name becomes `Unknown`
- malformed email becomes null
- missing email remains null
- missing city becomes `Unknown`
- customer types are standardized
- exact duplicate customer records are removed

## Customer Enrichment

Trusted orders are joined to trusted customers using `customer_id`.

A left join is used.

This keeps every trusted order even when a matching customer does not exist.

An inner join was not used because it would silently remove valid orders with unmatched customer IDs.

There are 35 trusted orders without a matching customer.

## Revenue Definition

Trusted revenue is calculated only from orders where:

`status = 'completed'`

Revenue is calculated as the sum of `total_amount`.

Pending, cancelled, refunded and unknown-status orders are not included in completed-order revenue.

## Order Value Classification

Orders are classified as:

- small: below $50
- medium: $50 to $199.99
- large: $200 to $499.99
- premium: $500 or more

Boundary values are also tested in the analysis notebook.

## Business Analysis

Notebook 3 answers the required management questions, including:

- completed-order count
- trusted completed revenue
- revenue by city
- revenue by product category
- top 10 orders
- average completed-order value
- order count by status
- most-used payment method
- top five cities by revenue
- revenue by customer
- top customers
- revenue by customer type
- unmatched customer orders
- order-size classification

## Spark SQL Cross-Check

Three results are calculated using both the DataFrame API and Spark SQL:

1. completed-order count
2. completed-order revenue
3. most frequently used payment method

The results are compared to confirm that both approaches produce the same answer.

## Transformations, Actions and Lazy Evaluation

Spark transformations create a new logical DataFrame but normally do not execute the full calculation immediately.

Examples from this project include:

`filter()`

`withColumn()`

`dropDuplicates()`

`groupBy()`

`join()`

Actions cause Spark to execute the required transformations and return a result.

Examples used in the notebooks include:

`count()`

`first()`

`collect()`

`display()`

Spark uses lazy evaluation.

For example:

`completed_orders = orders_silver.filter(F.col("status") == "completed")`

defines the transformation.

Spark does not need to fully evaluate it until an action such as:

`completed_orders.count()`

is executed.

At that point Spark runs the required transformation plan.

## Assumptions

The pipeline assumes:

- `order_id` should uniquely identify an order
- `customer_id` is the relationship key
- quantity and price must be positive
- invalid dates cannot be used in trusted orders
- malformed email does not invalidate an otherwise matchable customer
- exact duplicates can be safely removed
- `unknown` statuses should be reported rather than automatically deleted
- unmatched customer orders may still be valid for order-level reporting

## Known Limitations

22 trusted orders contain the status `unknown`.

35 trusted orders reference customers that do not exist in the customer dataset.

The pipeline currently uses CSV schema inference. In a production pipeline, an explicit schema would be safer.

The current dataset is relatively small. For much larger datasets, repeated Spark actions should be reduced and performance, partitioning and storage strategy would need more attention.

## How to Reproduce the Results

1. Place both raw CSV files in the configured Databricks Volume.
2. Run `01_data_exploration`.
3. Run `02_data_cleaning`.
4. Confirm that the validation checks pass.
5. Confirm that the two Silver tables are saved.
6. Run `03_business_analysis`.
7. Confirm that all three Spark SQL cross-checks match the DataFrame results.

The complete process can be rerun from the original raw files without manually editing the source data.