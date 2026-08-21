# Handover Questions

## 1. What was the most important data-quality problem?

The payment dates. A very large number of payments are dated before their referenced order, so they cannot be treated as trusted payment activity under the current business rules.

## 2. Which records were quarantined and why?

Records are quarantined when they contain duplicate or conflicting business IDs, invalid identifiers, invalid dates or statuses, invalid quantities or prices, inconsistent payment amounts, invalid parent references, or payment dates before the order date.

The original record and its validation reason are kept in `quarantine_records`.

## 3. What makes a record eligible for Silver?

It must pass its own field validation, duplicate checks and any relationship checks required by that entity. Silver contains trusted business entities, not just reformatted raw data.

## 4. What belongs in Gold?

Gold contains business outputs rather than cleaning logic: finance reconciliation, trusted revenue, revenue breakdowns, reconciliation counts and differences, and data-quality metrics.

## 5. How is expected order value calculated?

For each trusted order item, line value is `quantity * unit_price`. Those line values are summed for the order.

## 6. How is trusted revenue defined?

An order contributes trusted revenue only when it is a trusted Silver order, its status is `completed`, and its reconciliation result is `MATCHED`.

## 7. Why can order value and payment value disagree?

Common causes are underpayment, overpayment, failed or missing payments, refunds, invalid payment data, invalid order items, or timing and integration problems between source systems.

## 8. What join choices were used?

Left joins are used during validation when the source record must remain visible so a missing parent can be recorded as a quarantine reason.

Left-anti joins are useful during exploration for finding orphan references.

Finance reconciliation starts from trusted orders and left-joins order value and payment totals so orders without usable payments still remain visible.

Inner joins are used in trusted product reporting after the order has already qualified for trusted revenue.

## 9. What did `explain()` show?

The example in Notebook 3 shows Spark planning scans, filters, joins and aggregation before execution.

This connects to lazy evaluation because defining DataFrame transformations does not immediately run them. Spark builds the plan first, and an action such as `display()`, `count()` or a write triggers execution.

## 10. What would change with hundreds of millions of rows?

I would use incremental ingestion, Delta `MERGE`, fewer repeated full-table actions, carefully chosen join strategies, maintained table statistics, file compaction and clustering or partitioning based on actual query patterns.

I would also add orchestration, monitoring, checkpoints and data-quality alerts.
