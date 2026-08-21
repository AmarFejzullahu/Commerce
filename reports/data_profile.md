# Data Profile

The Commerce source contains five CSV files:

- Customers: 715 rows
- Products: 84 rows
- Orders: 4,555 rows
- Order items: 11,148 rows
- Payments: 4,072 rows

The files were inspected without editing the raw data.

## Main Findings

### Customers

The file contains 15 extra duplicate rows, 8 missing customer names and 12 malformed email values. Customer type and city formatting are also inconsistent.

These descriptive fields can be standardized without rejecting an otherwise valid customer because `customer_id` is the key used by the rest of the pipeline.

### Products

There are 4 extra duplicate rows and inconsistent category capitalization. No non-positive list prices were found.

Transaction `unit_price` is not forced to equal `list_price`, because an order can legitimately use a different sale price.

### Orders

The orders file contains 55 extra duplicate rows, 15 malformed dates, unexpected `unknown` statuses and customer references that do not exist in the customer source.

Order status and shipping-city formatting also vary and need standardization.

### Order Items

There are 40 extra duplicate rows, 92 rows with non-positive quantity and 61 rows with non-positive unit price.

Some rows also reference missing orders or products. More order items become invalid later if their parent order fails Silver validation.

### Payments

There are 30 extra duplicate rows and some payment amounts do not agree with their status.

The largest issue is payment timing. After duplicate removal and trusted-order matching, 1,893 payment rows are dated before the referenced order date. This is the main data-quality problem in the project.

## Conclusion

The raw exports should not be used directly for reporting. The main problems are duplicates, malformed order dates, invalid relationships, invalid order-item values and the large number of payments dated before their orders.
