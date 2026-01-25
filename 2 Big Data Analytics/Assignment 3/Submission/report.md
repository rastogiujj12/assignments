# 1. Introduction

FreshNest Retail is a medium-sized retail organisation operating multiple stores and warehouses across different regions. The company sells fast-moving consumer goods (FMCG) and relies heavily on multiple suppliers and third-party logistics providers. Over the past year, FreshNest has experienced recurring operational challenges, including frequent stockouts of popular products, excess inventory of slow-moving items, unreliable supplier deliveries, and rising logistics delays and costs.

Management believes that these issues are not solely operational but are strongly linked to fragmented data storage, poor integration across systems, and the absence of analytics-driven decision-making. Although FreshNest collects large volumes of operational data, this data is stored in isolated files or systems and is not analysed holistically. As a result, management lacks timely visibility into demand patterns, inventory risks, supplier reliability, and logistics performance.

This report presents a comprehensive Big Data Analytics Plan for FreshNest Retail. The objective is to design a scalable, analytics-driven framework that integrates multiple datasets, supports descriptive and predictive analytics, and enables dashboard-based decision support. The plan focuses on data understanding, ingestion and processing, analytics techniques, and visualisation strategies, with an optional extension to streaming analytics for enhanced operational responsiveness.

---

# 2. Task 1: Understanding the Case and Selected Research Questions

## 2.1 Business Problem Summary

FreshNest Retail faces inefficiencies across sales, inventory, supplier, and logistics operations due to siloed data and limited analytical capabilities. The lack of integrated analytics prevents proactive decision-making, leading to stock imbalances, supplier performance issues, and increased logistics costs. Senior management has requested the design of a big data analytics solution that can integrate datasets, provide operational visibility, support forecasting, and enable risk monitoring through dashboards.

## 2.2 Selected Research Questions

The following four research questions have been selected for this analytics plan:

1. How do sales demand patterns vary across time, products, and regions?
This question supports descriptive analytics to understand historical demand behaviour and identify temporal, product-level, and regional trends.
2. Which products and locations are at the highest risk of stockouts?
This question focuses on diagnostic analytics by combining demand and inventory data to detect imbalance risks and replenishment issues.
3. Which suppliers show consistent delivery delays or poor reliability?
This question evaluates supplier reliability by analysing delivery timeliness, fulfilment rates, and quality metrics. Logistics delivery data is used to attribute delays to either supplier-related or transport-related causes, while keeping the supplier as the unit of analysis.
4. Can historical data be used to forecast future demand and identify high-risk periods?
This question introduces predictive analytics by leveraging historical sales data to forecast demand trends and identify periods of elevated operational risk.

---

# 3. Task 2: Data Description and Understanding

## 3.1 Inventory Levels Dataset

* Purpose: Tracks inventory status across stores and warehouses over time.
* Key Variables: snapshot_date, product_id, location_id, location_type, stock_on_hand, reorder_point, safety_stock.
* Data Types: Dates, integers, categorical variables.
* Data Quality Issues: Missing stock values, negative stock levels, stock below safety stock, safety stock greater than reorder point, duplicate records by product-location-date.

## 3.2 Locations Dataset

* Purpose: Provides geographical and organisational context for stores and warehouses.
* Key Variables: location_id, location_name, location_type, region, country.
* Data Types: Categorical and text fields.
* Data Quality Issues: Minimal issues identified; serves as a reliable reference table.

## 3.3 Products Dataset

* Purpose: Stores product-level master data.
* Key Variables: product_id, product_name, category, unit_cost, shelf_life_days.
* Data Types: Numeric, categorical, text.
* Data Quality Issues: No major issues identified.

## 3.4 Sales Transactions Dataset

* Purpose: Records daily sales transactions at store level.
* Key Variables: transaction_id, date, product_id, store_id, quantity_sold, unit_price, promotion_flag.
* Data Types: Dates, numeric values, categorical flags.
* Data Quality Issues: Missing quantity_sold and unit_price, zero quantity sales, duplicate transaction IDs.

## 3.5 Supplier Performance Dataset

* Purpose: Captures supplier order fulfilment and quality metrics.
* Key Variables: supplier_id, product_id, order_date, expected_delivery_date, actual_delivery_date, units_ordered, units_received, defect_rate.
* Data Types: Dates, numeric fields.
* Data Quality Issues: Missing actual delivery dates, late deliveries, partial fulfilments.

## 3.6 Logistic Deliveries Dataset

* Purpose: Tracks shipment-level logistics performance.
* Key Variables: shipment_id, supplier_id, origin_location, destination_location, dispatch_date, arrival_date, planned_duration_hours, actual_duration_hours, transport_cost.
* Data Types: Dates, numeric fields.
* Data Quality Issues: Missing arrival dates, duplicate shipment IDs.

## 3.7 Dataset Integration

Datasets will be linked using common keys such as product_id, location_id, supplier_id, and dates. Sales and inventory data will be joined to assess stockout risk, while supplier performance and logistics datasets will be combined to analyse delivery delays and reliability.

---

# 4. Task 3: Data Ingestion and Processing Plan

## 4.1 Big Data Environment

The analytics solution will be implemented using Databricks with Apache Spark. All datasets will be stored as Delta tables to support scalable batch processing, schema enforcement, and reliable data versioning.

## 4.2 Research Question–Driven Processing Plan

**RQ1: Sales Demand Patterns**

Tables Required:

* sales_transactions
* products
* locations

Join Logic:

* sales_transactions.product_id = products.product_id
* sales_transactions.store_id = locations.location_id

Planned Transformations:

* Aggregate quantity_sold by day, week, and month
* Group demand by product category and region
* Create promotion-based demand indicators

Justification:
These transformations enable analysis of demand variation across time, products, and regions, directly addressing RQ1.

---

**RQ2: Stockout Risk**

Tables Required:

* inventory_levels
* sales_transactions
* products
* locations

Join Logic:

* inventory_levels.product_id = products.product_id
* inventory_levels.location_id = locations.location_id
* inventory_levels aligned with aggregated sales by product and location

Planned Transformations:

* Aggregate daily demand per product-location
* Feature engineering: stockout_flag, demand_velocity, inventory_coverage_days
* Filter or flag inconsistent inventory records (negative stock, safety stock violations)

Justification:
Combining demand and inventory metrics enables identification of products and locations at high risk of stockouts.

---

**RQ4: Supplier Reliability with Delay Attribution**

Tables Required:

* supplier_performance
* logistic_deliveries

Join Logic:

* supplier_performance.supplier_id = logistic_deliveries.supplier_id
* Temporal alignment between order, dispatch, and arrival dates

Planned Transformations:

* Calculate total delivery delay using expected and actual delivery dates
* Decompose delays into supplier-induced delays (late fulfilment or dispatch) and transport-induced delays (excess transit duration)
* Aggregate delay frequency, fulfilment rate, and defect rate per supplier

Justification:
Delay attribution enables accurate assessment of supplier reliability while distinguishing supplier-related issues from transport-related factors.

---

**RQ6: Demand Forecasting and High-Risk Periods**

Tables Required:

* sales_transactions
* products
* locations

Join Logic:

* Same joins as RQ1

Planned Transformations:

* Time-series aggregation of sales data (weekly and monthly)
* Rolling averages and seasonal indicators using window functions
* Identification of peak-demand and high-risk periods

Justification:
Historical sales patterns are essential for forecasting future demand and identifying periods of elevated operational risk.

---

# 5. Task 4: Data Analytics Plan

## 5.1 RQ1 – Sales Demand Patterns

* Analytics Type: Descriptive Analytics
* Techniques: Time-series aggregation, regional and category-based analysis, promotion impact analysis
* Spark Operations: groupBy aggregations, window functions for temporal analysis

---

## 5.2 RQ2 – Stockout Risk

* Analytics Type: Diagnostic Analytics
* Techniques: KPI development, threshold-based risk scoring
* Spark Operations: joins, conditional columns, aggregations, window functions

---

## 5.3 RQ4 – Supplier Reliability with Delay Attribution

* Analytics Type: Diagnostic Analytics
* Techniques: Delay decomposition, reliability scoring, defect and fulfilment analysis
* Spark Operations: date difference calculations, joins, groupBy aggregations

---

## 5.4 RQ6 – Demand Forecasting and High-Risk Periods

### 5.4.1 Batch Analytics for Forecasting

* Analytics Type: Predictive Analytics
* Techniques: Trend and seasonality analysis, rolling averages, demand forecasting logic
* Spark Operations: window functions, aggregations, time-based grouping

### 5.4.2 Optional Streaming Analytics Extension

While batch analytics enables demand forecasting using historical data, the same demand indicators can be monitored in near-real time using streaming analytics. Live sales events can be processed using Spark Structured Streaming to calculate rolling demand metrics and detect emerging demand spikes. This streaming extension reduces decision latency and enables early identification of high-risk periods without changing the underlying forecasting logic.

---

# 6. Task 5: Data Visualisation and Dashboard Plan

A management dashboard will be designed to provide actionable insights.

Key KPIs:

* Total and average sales by region and category
* Stockout risk percentage by location
* Supplier on-time delivery rate
* Average logistics delay and transport cost

Visualisations:

* Line charts for sales trends over time
* Heatmaps for stockout risk by product and location
* Bar charts for supplier delivery performance
* Scatter plots for logistics delay versus cost

These visualisations enable rapid interpretation and support data-driven decisions.

---

# 7. Optional: Streaming Data Analytics Plan

While the current solution focuses on batch analytics, FreshNest could extend the architecture to support real-time streaming analytics. Live sales transactions, inventory updates, and shipment status events could be ingested through a message broker and processed using Spark Structured Streaming in Databricks. Streaming analytics would enable real-time low-stock alerts, early detection of supplier delays, and immediate visibility into logistics disruptions, allowing proactive operational responses.

---

# 8. Conclusion

This report has presented a comprehensive Big Data Analytics Plan for FreshNest Retail, addressing key operational challenges related to sales demand variability, inventory stockouts, supplier reliability, and logistics delays. By integrating multiple datasets within a scalable Databricks and Apache Spark environment, the proposed solution enables robust data ingestion, processing, and analytics capabilities.

The analytics plan combines descriptive, diagnostic, and predictive techniques to transform fragmented operational data into actionable insights. In addition, the proposed dashboard design ensures that insights are communicated effectively to management, supporting informed and timely decision-making. The optional streaming analytics extension further demonstrates how FreshNest can evolve towards real-time, proactive operations.

Overall, the proposed big data analytics framework provides FreshNest Retail with a strong foundation for analytics-driven operational excellence and improved business performance.


