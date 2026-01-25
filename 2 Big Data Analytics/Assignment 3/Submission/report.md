# Big Data Analytics Planning for a Business: FreshNest Retail

## 1. Introduction

Modern retail organisations increasingly operate in data-intensive environments where large volumes of information are continuously generated across sales, inventory, logistics, and supplier operations. Effectively integrating and analysing this data is critical for maintaining optimal stock levels, ensuring supply chain reliability, reducing operational costs, and supporting informed decision-making. FreshNest Retail, a medium-sized retail company operating across multiple regions, has recently experienced frequent stockouts of high-demand products, excess inventory of slow-moving items, unreliable supplier deliveries, and rising logistics costs. Management believes these challenges stem from fragmented data storage and a lack of analytics-driven decision support.

This report presents a Big Data Analytics Plan for FreshNest Retail, adopting a distributed analytics approach aligned with modern enterprise data architectures. The plan focuses on the design of a scalable analytics solution using Apache Spark within the Databricks environment. Multiple operational datasets are analysed conceptually to address key business questions related to demand patterns, inventory risk, logistics performance, and supplier reliability. The report outlines data understanding, ingestion and processing strategies, analytical techniques, and a dashboard design to support management decision-making. An optional extension proposes how the batch analytics solution could be enhanced through real-time streaming analytics.

---

## 2. Understanding the Case and Selected Research Questions

### 2.1 Business Problem Summary

FreshNest Retail collects large volumes of operational data from sales systems, inventory management, logistics providers, and supplier records. However, this data is stored across disconnected systems and files, limiting the organisation’s ability to perform integrated analysis. As a result, the company struggles to anticipate demand, identify inventory risks, evaluate supplier performance, and manage logistics delays proactively. Senior management has requested the design of a big data analytics solution to integrate these datasets, improve operational visibility, and support data-driven decision-making.

### 2.2 Selected Research Questions

The following four research questions were selected based on their direct relevance to FreshNest Retail’s operational challenges and strategic objectives:

1. **How do sales demand patterns vary across time, products, and regions?**
   Understanding demand variability is essential for accurate forecasting, inventory planning, and promotion evaluation.

2. **Which products and locations are at the highest risk of stockouts?**
   Identifying stockout risk enables proactive replenishment and reduces lost sales opportunities.

3. **Which suppliers show consistent delivery delays or poor reliability?**
   Evaluating supplier performance helps mitigate supply chain risks and supports contract and sourcing decisions.

4. **What are the main factors contributing to logistics delays and increased delivery times?**
   Analysing logistics performance supports cost control and delivery reliability across the network.

---

## 3. Data Description and Understanding

### 3.1 Overview of Available Data

The datasets provided for this assignment represent a simplified but realistic snapshot of a retail organisation’s operational data landscape. Each dataset captures a different functional aspect of FreshNest Retail’s operations, and together they enable integrated analysis across sales, inventory, logistics, and supplier performance. Understanding the scope, structure, and limitations of each dataset is a critical step in designing a robust analytics plan.

The data is structured, relational in nature, and suitable for distributed processing using Spark. However, as with many real-world datasets, it contains inconsistencies and quality issues that must be addressed before meaningful analytics can be performed.

### 3.1 Datasets and Purpose

* **Sales Transactions**: Records individual sales transactions, used to analyse demand patterns across products, stores, and time.
* **Inventory Levels**: Snapshot-based inventory data used to assess stock availability, safety stock compliance, and stockout risk.
* **Products**: Contains product attributes such as category, unit cost, and shelf life.
* **Locations**: Stores and warehouses with regional and geographic attributes.
* **Logistic Deliveries**: Shipment-level data used to analyse delivery durations and transport costs.
* **Supplier Performance**: Supplier order and delivery records used to evaluate reliability and defect rates.

### 3.2 Key Variables and Data Types

Key variables include dates and timestamps (transaction dates, shipment dates), numerical measures (quantity sold, stock levels, delivery durations, costs), and categorical identifiers (product IDs, location IDs, supplier IDs, regions, categories). These data types support temporal analysis, aggregation, joins, and performance evaluation.

### 3.3 Data Quality Considerations

Initial exploratory analysis revealed several data quality issues that are typical of large-scale operational datasets within enterprise environments. Missing values were identified in key numerical and date fields such as stock_on_hand, quantity_sold, unit_price, arrival_date, and actual_delivery_date. Duplicate records were observed when grouping inventory data by product, location, and snapshot date, as well as duplicate identifiers in sales transactions and logistics shipments.

Logical inconsistencies were also detected, including negative stock_on_hand values, cases where safety_stock exceeded reorder_point thresholds, and inventory levels falling below defined safety stock. Zero-quantity sales records were present, indicating either data entry errors or cancelled transactions that were not filtered upstream. Despite these issues, no orphan records were found across datasets, and logistics timing relationships and supplier delivery logic were internally consistent.

Addressing these data quality issues is essential to ensure the reliability of downstream analytics and highlights the importance of data validation within a big data processing pipeline.

Addressing these data quality issues is critical to ensure analytical validity, as unresolved inconsistencies could distort inventory risk assessment, supplier performance evaluation, and demand trend analysis. The findings from this exploratory assessment directly inform the data cleaning and validation strategies described in subsequent sections.

### 3.4 Dataset Integration

Datasets are linked using shared keys such as product_id, location_id, store_id, and supplier_id. This integration enables cross-functional analysis, such as linking sales demand to inventory levels and supplier delivery performance.

---

## 4. Data Ingestion and Processing Plan

### 4.1 Storage Strategy

All datasets are stored as structured tables within the Databricks environment using a distributed storage layer designed to support scalability, reliability, and efficient query execution. This approach supports scalability, fault tolerance, and efficient querying.

### 4.2 Data Loading

Batch data ingestion is performed using Spark DataFrames and Spark SQL, allowing efficient loading of large datasets and schema enforcement.

### 4.3 Data Cleaning and Preprocessing

Planned preprocessing steps include handling missing values through filtering or imputation, removing or consolidating duplicate records, validating logical constraints (such as non-negative stock levels), and standardising date formats. These steps ensure data reliability for downstream analytics.

### 4.4 Transformations and Feature Engineering

Key transformations include joins across datasets, aggregations at product, location, and time levels, and derived metrics such as stockout risk indicators, delivery delays, and supplier reliability scores. These transformations directly support the selected research questions.

Feature engineering choices are driven by the need to translate raw operational data into analytically meaningful indicators aligned with business objectives. Derived features such as stockout risk flags, delivery delay metrics, and supplier reliability indicators enable direct assessment of operational performance against defined thresholds and expectations. These features reduce analytical complexity while supporting clearer interpretation of results by business stakeholders.

---

## 5. Data Analytics Plan

### 5.1 Sales Demand Analysis

This research question primarily involves **descriptive analytics**, with elements of exploratory analysis to uncover temporal and regional patterns. Sales data is analysed at daily and weekly aggregation levels to identify short-term demand fluctuations, seasonal patterns, and the impact of promotional activity, providing a temporal foundation for inventory and replenishment planning. Techniques include time-series aggregation, trend analysis, and regional comparisons. Spark SQL functions such as GROUP BY, window functions, and date-based aggregations are used. Python libraries such as Pandas and Matplotlib may support further analysis and visualisation.

### 5.2 Stockout Risk Identification

A combination of **descriptive and diagnostic analytics** is applied to identify products and locations where stock_on_hand falls below safety_stock or reorder_point thresholds. Conditional logic, joins, and aggregations are used to compute risk indicators.

### 5.3 Supplier Reliability Analysis

**Diagnostic analytics** is used to evaluate supplier performance through delivery delays, units received versus ordered, and defect rates. Correlation analysis and KPI comparisons support supplier assessment.

### 5.4 Logistics Delay Analysis

Logistics performance is analysed using **descriptive and diagnostic analytics**, focusing on planned versus actual delivery durations and transport costs. Aggregations by route, supplier, and region help identify delay patterns.

---

## 6. Data Visualisation and Dashboard Plan

The primary objective of the dashboard is to translate complex analytical outputs into intuitive, actionable insights for senior management and operational teams. The dashboard is designed to provide both high-level oversight and the ability to drill down into specific products, locations, suppliers, or time periods.

Key KPIs displayed on the dashboard include total and average sales volumes, stockout risk indicators, percentage of inventory below safety stock, average supplier delivery delays, logistics cost per shipment, and supplier defect rates. These KPIs enable rapid assessment of operational performance.

Each KPI is explicitly aligned with the selected research questions. Sales trend and volume metrics address demand variability across products and regions, stockout risk indicators support identification of high-risk inventory locations, supplier delay and defect metrics evaluate supplier reliability, and logistics cost and duration KPIs highlight factors contributing to delivery inefficiencies.

Visualisations are selected based on the nature of the data and the analytical questions being addressed. Line charts are used to represent sales demand trends over time, enabling the identification of seasonality and promotional effects. Heatmaps display stockout risk across locations and products, highlighting high-risk areas at a glance. Bar charts and ranking tables are used to compare supplier performance and logistics delays, while KPI tiles provide summary metrics for executive-level reporting.

By consolidating these visual elements into a single dashboard, management can monitor operational health, identify emerging risks, and support data-driven decision-making.

The dashboard is designed to provide senior management and operational stakeholders with a high-level yet actionable overview of business performance. Key KPIs include sales volume trends, stockout risk indicators, average delivery delays, and supplier reliability scores. Visualisations include line charts for demand trends, heatmaps for stockout risk across locations, bar charts for supplier performance, and summary KPI tiles. These visualisations support rapid interpretation and informed decision-making.

---

## 7. Optional: Streaming Data Analytics Plan

While the current analytics solution is designed around batch processing of historical data, FreshNest Retail could further enhance its decision-making capabilities by adopting a real-time streaming analytics approach. Streaming analytics is particularly relevant in retail environments where operational conditions can change rapidly due to demand fluctuations, supply disruptions, or logistics delays.

In a proposed streaming architecture, real-time sales transactions from point-of-sale systems, live inventory updates from warehouses, and shipment status events from logistics partners could be ingested through a message broker such as Apache Kafka or a cloud-based event hub. These event streams would be processed using Spark Structured Streaming within the Databricks environment, enabling continuous aggregation, window-based analytics, and stateful computations.

Key streaming use cases include real-time alerts when inventory levels fall below safety stock thresholds, early detection of supplier delivery delays based on deviations from planned delivery durations, and live monitoring of sales spikes during promotional campaigns. Processed streaming outputs could be written to Delta tables and integrated into existing dashboards to provide near-real-time operational visibility.

By combining batch and streaming analytics, FreshNest Retail would transition from reactive reporting to proactive operational management, aligning with modern big data analytics best practices and supporting faster, data-driven responses to emerging risks.

While the current solution is based on batch processing, FreshNest Retail could enhance its analytics capabilities through real-time streaming analytics. Live sales transactions, inventory updates, and shipment status events could be ingested via a message broker and processed using Spark Structured Streaming in Databricks. Streaming analytics would enable real-time low-stock alerts, early detection of supplier delays, and immediate monitoring of promotional impacts. Integrating streaming with batch analytics would allow FreshNest Retail to move from reactive reporting to proactive operational management.

---

## 8. Ethical and Legal Considerations

Although the datasets used in this assignment are synthetic and do not contain real personal or commercially sensitive information, ethical and legal considerations remain central when designing big data analytics solutions. In real-world retail environments, sales and logistics data may indirectly reveal customer behaviour, supplier performance, or employee activities, raising concerns around data privacy, fairness, and responsible use.

Organisations must ensure compliance with relevant data protection regulations, such as the General Data Protection Regulation (GDPR), particularly when handling customer-related or location-based data. This includes implementing appropriate access controls, data minimisation practices, and secure storage mechanisms. Transparency in analytics processes is also essential to ensure that automated insights or performance evaluations do not lead to unfair treatment of suppliers or operational staff.

From an ethical perspective, analytics-driven decisions should be explainable and proportionate. For example, supplier performance assessments should consider data quality limitations and contextual factors rather than relying solely on automated metrics. Embedding ethical and legal considerations into the analytics design phase supports responsible data use and long-term organisational trust.

---

## 9. Conclusion

This report presented a comprehensive Big Data Analytics Plan for FreshNest Retail, addressing key operational challenges through an integrated and scalable analytics approach. By leveraging Databricks and Apache Spark, the proposed solution supports efficient data ingestion, quality assessment, transformation, and analysis across multiple operational domains. The analytics plan enables improved visibility into sales demand, inventory risk, supplier reliability, and logistics performance, while the dashboard design ensures business-appropriate communication of insights. The optional streaming extension further demonstrates how the solution can evolve toward real-time, proactive decision support, aligning with modern big data analytics best practices.

The inclusion of an optional streaming analytics plan demonstrates how the proposed batch-based solution can be extended to support near-real-time monitoring and proactive decision-making. Overall, the report highlights the strategic value of big data analytics in modern retail operations and provides a robust foundation for the subsequent implementation phase in Assignment 4.