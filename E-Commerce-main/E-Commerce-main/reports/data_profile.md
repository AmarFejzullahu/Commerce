# Data Profile

## Overview

The project uses two raw CSV files:

- `orders_raw.csv` with 8,085 data records
- `customers_raw.csv` with 1,225 data records

The header row is not included in Spark's record count.

The raw files were inspected before any cleaning was applied.

## Orders

The orders dataset contains:

`order_id`, `customer_id`, `order_date`, `city`, `product_category`, `quantity`, `unit_price`, `status`, `payment_method`

The main issues found were:

- 46 records with missing `customer_id`
- 53 records with invalid quantity values of `0` or `-1`
- 34 records with invalid unit prices of `0` or `-25`
- 18 records with the invalid date `2026-99-40`
- 25 records with missing city
- 15 records with missing product category
- 18 records with missing payment method
- 85 order IDs appearing more than once

The duplicated order IDs were investigated and were exact duplicate records rather than different versions of the same order.

Order status values were inconsistent because of capitalization, whitespace and different representations of the same status. Examples included `Completed`, `COMPLETED`, `complete`, `cancel`, `canceled` and `cancelled`.

There were also 22 records with the status `unknown`.

Payment methods had similar inconsistencies, including different forms of PayPal, bank transfer, credit card and cash.

City names also contained spelling and formatting variations such as:

- `Prishtina` and `Prishtinë`
- `Gjakove` and `Gjakovë`
- `Peja` and `Pejë`
- `Mitrovice` and `Mitrovicë`
- `Vushtrri` and `Vushtrria`

These values need to be standardized before grouping revenue by city.

## Customers

The customers dataset contains:

`customer_id`, `customer_name`, `email`, `city`, `customer_type`

The main issues found were:

- 16 missing customer names
- 29 missing emails
- 22 malformed emails
- 15 missing cities
- 25 duplicated customer IDs
- 10 records with customer type `unknown`

The duplicate customer IDs were exact duplicates.

Email was not treated as the relationship key because orders and customers are matched using `customer_id`.

## Customer References

There are 35 customer IDs used by orders that do not exist in the customer dataset.

These orders can still be used for order-level reporting, but they cannot be fully enriched with customer information.

## Conclusion

The raw datasets should not be used directly for reporting.

The main problems are invalid numeric values, malformed dates, duplicate records, inconsistent business values and missing customer references.