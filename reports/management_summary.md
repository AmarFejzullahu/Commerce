# Management Summary

The raw Commerce exports are not reliable enough to use directly for Finance reporting.

The pipeline found duplicate rows, malformed order dates, invalid relationships, invalid order-item values and a major payment-date issue. After validation, the trusted Silver layer contains 700 customers, 80 products, 4,409 orders, 10,696 order items and 2,058 payments.

Payments are the main concern. Only 50.54% of raw payment rows reach trusted Silver under the current rule that a payment cannot occur before its order date.

Finance reconciliation identifies matched, underpaid, overpaid, missing-payment, refunded and non-standard orders. Trusted revenue is limited to completed orders with matched trusted payment activity.

Using that conservative rule, the current data produces 1,203 trusted revenue orders and approximately $1,549,918.12 in trusted revenue.

Finance can use this Gold trusted-revenue value as a reproducible and validated figure. It should not be treated as the full possible revenue in the raw exports until the payment-date problem is understood.

The first source-system issue to investigate is why so many payments occur before their order dates and whether those values are incorrect timestamps, test data or a real prepayment process that has not been documented.
