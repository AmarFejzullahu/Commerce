# Data Quality Summary

After the Silver validation rules are applied:

- Customers: 715 raw, 15 rejected, 700 trusted. Success rate: 97.90%.
- Products: 84 raw, 4 rejected, 80 trusted. Success rate: 95.24%.
- Orders: 4,555 raw, 146 rejected, 4,409 trusted. Success rate: 96.79%.
- Order items: 11,148 raw, 452 rejected, 10,696 trusted. Success rate: 95.95%.
- Payments: 4,072 raw, 2,014 rejected, 2,058 trusted. Success rate: 50.54%.

Overall, 17,943 of 20,574 source records reach trusted Silver, about 87.21%.

The payment dataset is the clear quality concern. Most rejected payment rows are caused by payment dates occurring before the trusted order date.

Other important rejection reasons include duplicate rows, invalid order dates, unexpected statuses, invalid customer/order/product references, non-positive quantities or prices, and payment amounts that do not match payment status.

Some records can fail more than one rule, so individual reason counts should not be added together to calculate the number of rejected rows.

All rejected records remain available in `quarantine_records` with their original values and validation reason. This keeps the Silver layer trusted without losing traceability back to the source.
