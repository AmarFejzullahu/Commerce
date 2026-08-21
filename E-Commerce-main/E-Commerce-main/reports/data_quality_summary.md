# Data Quality Summary

## Orders

The raw orders dataset contained 8,085 records.

151 invalid records were identified because they contained one of the following:

- missing customer ID
- invalid order date
- quantity less than or equal to zero
- unit price less than or equal to zero

After removing invalid records, 7,934 valid order records remained.

82 exact duplicate records were then removed.

The final trusted orders dataset contains 7,852 records.

This gives an order cleaning success rate of approximately 97.12%.

The trusted dataset contains:

- no duplicate order IDs
- no missing required order IDs
- no missing required customer IDs
- no invalid quantities
- no invalid unit prices
- no invalid order dates
- valid `total_amount` values calculated as quantity multiplied by unit price

Valid records were also standardized instead of being removed unnecessarily.

This included:

- normalizing order statuses
- normalizing payment methods
- standardizing city names
- standardizing product categories
- replacing missing grouping values with `Unknown`

22 orders still have the normalized status `unknown`.

These records were kept because an unusual status alone does not prove that the rest of the order data is invalid. They are not included in completed-order revenue.

## Customers

The raw customers dataset contained 1,225 records.

25 exact duplicate records were removed, leaving 1,200 trusted customer records.

Missing or malformed descriptive information was cleaned without removing an otherwise usable customer.

The following rules were used:

- missing customer name becomes `Unknown`
- malformed email becomes null
- missing email remains null
- missing city becomes `Unknown`
- customer type is standardized
- exact duplicates are removed

The final customers dataset contains no duplicate customer IDs.

## Customer Matching

35 trusted orders reference customer IDs that do not exist in the customer dataset.

These orders are kept for order-level analysis but cannot be fully enriched with customer information.

## Final Result

The cleaned datasets contain:

- 7,852 trusted orders
- 1,200 trusted customers
- 35 orders without a matching customer

The cleaned order dataset is suitable for the required reporting, with the remaining `unknown` statuses and unmatched customer references documented as known data-quality issues.