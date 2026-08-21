# Management Summary

The raw e-commerce exports contained several issues that made them unreliable for direct reporting. These included duplicate records, invalid quantities and prices, malformed dates, inconsistent status and payment-method values, city-name variations and missing customer references.

The pipeline received 8,085 order records and 1,225 customer records.

After validation, cleaning and duplicate removal, the final trusted datasets contain:

- 7,852 trusted orders
- 1,200 trusted customers

The order cleaning success rate is approximately 97.12%.

A total of 233 order records were excluded. This included 151 invalid records and 82 exact duplicates.

The trusted dataset contains 5,150 completed orders with total completed-order revenue of $3,844,023.85.

The average completed-order value is $746.41.

Prizren generated the highest trusted revenue at $509,447.02.

Sports was the strongest product category with $516,567.23 in trusted revenue.

Credit Card was the most frequently used payment method, used in 1,999 trusted orders.

Two data-quality limitations remain:

- 22 trusted orders have an `unknown` status
- 35 trusted orders cannot be matched to a customer record

The cleaned dataset can be used for the requested reporting, but the source system should improve validation before data is exported.

The first improvement should be to enforce valid customer IDs, dates, quantities, prices, statuses and payment methods when records are created. This would reduce the need for downstream cleaning and improve future reporting reliability.