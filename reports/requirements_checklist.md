# Requirements Checklist

## Architecture and Storage

- [x] Bronze, Silver and Gold layers are separated.
- [x] Explicit Spark schemas are used for CSV ingestion.
- [x] Raw source files remain unchanged.
- [x] Dates stay as strings in Bronze and are parsed in Silver.
- [x] Trusted Silver outputs are stored as Delta tables.
- [x] Gold outputs are stored as Delta tables.
- [x] Architecture diagram is included.

## Validation and Traceability

- [x] Identifiers and required fields are validated.
- [x] Duplicate business identifiers are investigated.
- [x] Numeric values and dates are validated.
- [x] Order and payment statuses are validated.
- [x] Customer, order, product and payment relationships are checked.
- [x] Invalid records are preserved in quarantine with reasons.
- [x] Business rules that are not obvious from the source are documented.

## Finance and Gold Outputs

- [x] Expected order value is calculated from trusted order items.
- [x] Matched, underpaid and overpaid orders are identified.
- [x] Missing-payment, refunded and non-standard cases are identified.
- [x] Trusted revenue is calculated from trusted data only.
- [x] Revenue can be analyzed by date, city, product, category, customer and customer type.
- [x] Largest reconciliation differences are available for investigation.
- [x] Source, rejected and trusted counts are included in the data-quality summary.

## Spark and Handover

- [x] A meaningful transformation is inspected with `explain()`.
- [x] Lazy evaluation is explained in the README.
- [x] Notebook execution order is documented.
- [x] Management summary is included.
- [x] Handover questions are answered.
- [x] One order can be followed through the pipeline and explained live.
