# Big Data Analytics Planning for a Business: FreshNest Retail

## 1. Introduction

Modern retail businesses generate large amounts of data from sales, inventory, logistics, and supplier operations. When this data is properly integrated and analysed, it helps companies maintain optimal stock levels, improve supply chain reliability, reduce costs, and make better decisions. However, when systems are disconnected, organisations struggle to turn raw data into useful insights.

FreshNest Retail, a medium-sized retailer operating across multiple regions, faces several operational challenges, including stockouts of popular products, excess inventory of slow-moving items, unreliable supplier deliveries, and rising logistics costs. These issues are linked not only to operations but also to limited use of integrated analytics. Data exists across different systems, but it is fragmented and contains quality issues such as missing values and inconsistencies.

This report presents a Big Data Analytics Plan using Apache Spark in Databricks and a Bronze–Silver–Gold architecture. The plan focuses on building a scalable data pipeline that cleans, validates, and transforms raw data into business-ready metrics. Key areas of analysis include demand patterns, stockout risk, supplier reliability, and demand forecasting. While the main focus is batch analytics, the design also considers future extension to simulated streaming for more timely decision-making.

---

## 2. Understanding the Case and Selected Research Questions
### 2.1 Business Problem Summary

FreshNest Retail collects large volumes of operational data from sales transactions, inventory systems, logistics deliveries, and supplier performance records. However, these datasets are stored in separate systems and are not analysed together. This makes it difficult for the company to gain a full view of operations. In addition, the data contains quality issues such as missing values and inconsistent records, which further limit reliable analysis.

Because of this, FreshNest Retail struggles to anticipate product demand, identify stockout risks early, assess supplier reliability, and forecast high-risk periods. Many decisions are made reactively instead of proactively. Senior management has therefore requested a big data analytics solution that can integrate these datasets, clean and transform the data, and generate meaningful business metrics to improve operational visibility and support data-driven decisions.

### 2.2 Selected Research Questions

To address these challenges, four research questions were selected:

1. How do sales demand patterns vary across time, products, and regions?

2. Which products and locations are at the highest risk of stockouts?

3. Which suppliers show consistent delivery delays or poor reliability?

4. Can historical data be used to forecast future demand and identify high-risk periods?

Each question leads to specific analytical outputs and KPIs that will be produced through the data processing pipeline.

---

## **3. Data Description and Understanding**

### **3.1 Overview of Available Data**

The datasets provided represent a simplified but realistic view of a retail company’s operations. Each dataset covers a different functional area, including sales, inventory, logistics, suppliers, products, and locations. Together, they allow integrated analysis across the full supply chain. Understanding the structure and limitations of these datasets is important for designing an effective analytics pipeline.

The data is structured and relational, making it suitable for distributed processing using Apache Spark. However, the datasets contain several inconsistencies and quality issues that must be addressed before reliable analysis can be performed.

### **3.2 Datasets and Purpose**

* **Sales Transactions:** Records individual sales events across products, stores, and time, used to analyse demand patterns.
* **Inventory Levels:** Snapshot data showing stock levels, reorder points, and safety stock, used to evaluate stock availability and stockout risk.
* **Products:** Contains product attributes such as category and cost.
* **Locations:** Provides geographic information for stores and warehouses.
* **Logistics Deliveries:** Shipment-level data used to assess delivery times and transport performance.
* **Supplier Performance:** Records supplier orders, deliveries, and defect rates, used to evaluate reliability.

### **3.3 Key Variables and Data Types**

The data includes timestamps (sales dates, shipment dates), numerical measures (quantity sold, stock levels, delivery durations), and categorical identifiers (product IDs, location IDs, supplier IDs). These variables support aggregation, joins, and time-based analysis.

### **3.4 Data Quality Considerations**

Exploratory analysis identified missing values in important fields such as stock levels, quantity sold, unit price, and delivery dates. Duplicate records were also found in several tables. Logical issues were observed, including negative stock levels, safety stock higher than reorder points, and zero-quantity sales. These issues can distort business insights if not handled properly. Therefore, the analytics pipeline includes data cleaning, deduplication, validation rules, and standardisation of date formats before analysis.

### **3.5 Dataset Integration**

Datasets are linked using shared keys such as product_id, location_id, store_id, and supplier_id. These relationships enable cross-functional analysis, such as connecting sales demand with inventory levels and supplier performance.

---

## 4. Data Ingestion and Processing Plan
### 4.1 Storage Strategy

Using Spark enables distributed processing, allowing large retail datasets to be processed efficiently and making the architecture scalable as data volumes grow. 

All datasets are stored as structured Delta tables in the Databricks environment using a distributed storage system. A Medallion architecture is used to organise the data into three layers: Bronze (raw data), Silver (cleaned and structured data), and Gold (business-ready analytics). This layered approach improves scalability, data reliability, and simplified maintenance.

### 4.2 Data Loading

Raw datasets are first ingested into the Bronze layer using Spark DataFrames. Historical data (2019–2024) is processed in batch mode, while 2025 data is treated as daily micro-batches to simulate streaming. Each batch is tagged with ingestion timestamps to support time-based processing.

### 4.3 Data Cleaning and Preprocessing

Data cleaning is performed in the Silver layer. This includes removing duplicate records, handling missing values through filtering or imputation depending on the variable importance, validating logical constraints (e.g., non-negative stock levels), correcting inconsistent formats, and standardising date and timestamp fields. Table-specific validation rules are applied to ensure realistic operational data.

### 4.4 Transformations and Feature Engineering

After cleaning, datasets are joined and transformed to create analytical features. Examples include stockout risk flags, delivery delay metrics, supplier reliability indicators, and sales performance measures. Aggregations are performed at product, location, and daily levels to generate structured analytical tables. These transformations convert raw operational data into KPIs that directly support the research questions. The resulting aggregated KPI tables are stored in the Gold layer, where they are optimised for reporting, dashboard visualisation, and decision-making.

---

## 5. Data Analytics Plan
### 5.1 Sales Demand Analysis

This research question focuses on descriptive analytics to understand how sales demand changes across time, products, and regions. Cleaned sales data from the Silver layer is aggregated at daily and weekly levels. Spark SQL functions such as GROUP BY and window operations are used to identify demand trends, seasonal patterns, and differences across product categories and geographic regions. Promotional activity is also analysed to measure its effect on sales volume. These insights support better inventory and replenishment planning.

## 5.2 Stockout Risk Identification

This analysis identifies products and locations at risk of stockouts. Inventory data is combined with product and location information, and rule-based indicators are used to flag risk situations, such as stock levels falling below safety stock or reorder points. Aggregations help measure how frequently stockouts occur across locations. This provides early warning indicators for inventory management.

### 5.3 Supplier Reliability Analysis

Supplier performance is evaluated using delivery delays, fill rates, and defect rates derived from supplier and logistics data. Spark transformations calculate delay durations and reliability metrics, which are aggregated to create supplier performance KPIs. This helps identify suppliers with consistent delivery issues.

### 5.4 Forecasting Demand and High-Risk Periods

Historical sales trends are used to estimate future demand using time-based trend analysis. Rolling averages and trend patterns are calculated in Spark to forecast expected demand. These forecasts are compared with inventory levels to identify potential high-risk periods for stockouts. For 2025 data, forecasts are updated through daily micro-batches to simulate streaming-based analytics.

Simple time-series techniques such as rolling mean smoothing and trend-based extrapolation are used rather than complex machine learning models, as the objective is operational planning and interpretability rather than advanced predictive modelling.

Key outputs include forecasted demand, predicted stockout periods, and peak-demand alerts.

---

## 6. Data Visualisation and Dashboard Plan

The dashboard is designed to convert analytical results into clear and actionable insights for managers and operational staff. It combines outputs from the Gold layer tables, where cleaned and aggregated data is stored in KPI-ready form. The dashboard provides both high-level summaries and the ability to explore details by product, region, supplier, or date.

Key KPIs displayed include:

- **Sales demand metrics:** Total units sold, average sales value, daily trends, and seasonal patterns across regions and product categories.

- **Stockout risk indicators:** Percentage of products below safety stock, number of critical stock situations, and locations with frequent risk.

- **Supplier performance metrics:** Average delivery delay, fill rate, and defect rate to evaluate supplier reliability.

- **Forecasting KPIs:** Predicted demand levels, expected high-risk periods for stockouts, and peak-demand alerts.

Visualisations are chosen to match the type of analysis:

- **Line charts** show sales trends and forecasted demand over time.

- **Heatmaps** highlight regions or products with high stockout risk.

- **Bar charts** and ranking tables compare supplier performance.

- **KPI tiles** summarise important numbers for quick management review.

For Q2 and Q6, dashboards can be updated using daily micro-batch data to simulate near-real-time monitoring.

These visual insights support operational decisions such as stock redistribution, supplier review, and promotional planning.

---

## 7. Simulated Streaming Data Analytics Plan

Although the main solution is based on batch processing, this project also includes a simulated streaming component to demonstrate how analytics could become more timely. Instead of using live data sources, streaming is represented through daily micro-batches derived from the most recent portion of the dataset. This approach allows the system to mimic how new operational data would arrive in real business environments.

For this simulation, earlier historical data is treated as batch input, while recent daily data is processed incrementally. Each micro-batch updates key analytical tables in the Silver and Gold layers. This is particularly useful for:

Q2 (Stockout Risk): Daily updates allow identification of locations where stock levels become critical.

Q6 (Demand Forecasting): Forecast calculations are refreshed as new sales data arrives, improving prediction accuracy for upcoming periods.

Although this is not real-time streaming with technologies such as Kafka, the micro-batch approach demonstrates how Spark-based systems can transition from batch analytics to near-real-time monitoring. This design shows how FreshNest Retail could move toward more proactive, data-driven decision-making in the future.

---

## 8. Ethical and Legal Considerations

Although the datasets used in this assignment are synthetic and do not include real personal or commercial data, ethical and legal issues are still important when designing big data analytics systems. In real retail environments, sales, location, and supplier data can indirectly reveal sensitive information about customer behaviour, business performance, or operational staff. This creates risks related to data privacy, fairness, and responsible decision-making.

Retail organisations must comply with data protection regulations such as the General Data Protection Regulation (GDPR). Key practices include restricting access to sensitive data, storing information securely, and collecting only data that is necessary for analysis. The layered data architecture used in this project (Bronze, Silver, Gold) supports this by separating raw data from business-ready outputs and reducing unnecessary data exposure. Data governance policies, including role-based access control and data lineage tracking, are also essential to ensure accountability in large-scale analytics systems.

Ethical concerns also arise when analytics results influence decisions. For example, supplier performance scores or demand forecasts should not be treated as perfect truths. Data quality issues, missing values, and modelling assumptions can affect results. Therefore, insights should be interpreted carefully and supported by human judgement to avoid unfair or biased decisions. Embedding these considerations into system design promotes responsible and trustworthy analytics.

---

## 9. Conclusion

This report presented a Big Data Analytics Plan for FreshNest Retail aimed at addressing key operational challenges through an integrated and scalable data architecture. Using Apache Spark within the Databricks environment, the proposed solution enables structured data ingestion, cleaning, transformation, and analysis across sales, inventory, supplier, and logistics datasets. The analytics framework supports better visibility into demand trends, stockout risks, supplier reliability, and future demand patterns, helping management make more informed and proactive decisions.

The Medallion architecture combined with micro-batch processing illustrates how batch and near-real-time analytics can be integrated within a scalable big data framework.

The dashboard design ensures that analytical results are communicated clearly to business users through meaningful KPIs and visualisations. In addition, the project introduces a simulated streaming approach using daily micro-batches, demonstrating how the system could move toward more timely updates for stockout monitoring and demand forecasting.

This plan shows how big data technologies can be applied in a retail context to improve operational efficiency and decision-making. It also provides a strong foundation for the technical implementation phase in the next stage of the assignment.
