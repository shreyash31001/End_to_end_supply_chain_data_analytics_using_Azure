# End_to_end_supply_chain_data_analytics_using_Azure

# Azure Supply Chain Analytics Pipeline & Power BI Dashboard

## Overview

This project demonstrates an end-to-end cloud-based analytics workflow for supply chain data using **Microsoft Azure and Power BI**.

The goal was not only to create a dashboard, but to build a workflow that takes raw operational data through ingestion, transformation, data-quality validation, feature engineering, and finally business analysis.

### Architecture

```text
Raw Supply Chain Data
        │
        ▼
Azure Blob Storage
   /raw
        │
        ▼
Azure Data Factory
        │
        ▼
Mapping Data Flow
 ├── Data Type Conversion
 ├── Data Quality Checks
 └── Feature Engineering
        │
        ▼
Azure Blob Storage
   /processed
        │
        ▼
Power BI
        │
        ▼
Supply Chain Performance Dashboard
```

---

## Business Problem

Supply chain teams need visibility into multiple areas of operations at the same time:

- Which product categories generate the most revenue?
- How does inventory availability compare with order quantities?
- How do manufacturing costs and defect rates vary?
- Which suppliers and locations require further investigation?
- How do transportation costs differ across transportation modes?

The objective of this project was to transform raw supply chain data into business-ready information that can help stakeholders identify areas requiring deeper operational investigation.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| **Azure Blob Storage** | Storage for raw and processed supply chain data |
| **Azure Data Factory** | Data pipeline orchestration |
| **ADF Mapping Data Flow** | Data transformation and feature engineering |
| **Power BI** | Data modeling, analysis, and dashboard development |
| **DAX** | Creation of dashboard measures and KPIs |
| **CSV** | Source and processed data format |

---

## Data Pipeline

The project follows a simple ETL architecture:

```text
Extract
Raw CSV → Azure Blob Storage

Transform
Azure Blob Storage → Azure Data Factory Mapping Data Flow

Load
Processed Dataset → Azure Blob Storage → Power BI
```

The raw dataset is preserved separately from the processed dataset so that transformations do not modify the original source data.

---

## Data Processing

### 1. Data Type Validation

The raw CSV initially contained fields that required explicit type conversion.

Numeric columns such as:

- Price
- Availability
- Number of products sold
- Revenue generated
- Stock levels
- Lead times
- Order quantities
- Shipping times
- Shipping costs
- Production volumes
- Manufacturing lead time
- Manufacturing costs
- Defect rates
- Transportation costs

were converted into appropriate integer or decimal data types using Azure Data Factory Mapping Data Flows.

---

### 2. Duplicate / SKU Validation

SKU was treated as the product-record identifier.

An aggregation check was performed by grouping records by SKU and calculating the number of records associated with each SKU.

The validation showed:

> **100 records and 100 unique SKUs**

No duplicate SKU records were identified.

---

### 3. Categorical Consistency

Categorical fields were profiled using aggregation checks to identify inconsistent labels.

Fields examined included:

- Product Type
- Customer Demographics
- Shipping Carrier
- Supplier
- Location
- Inspection Result
- Transportation Mode
- Route

The observed categories were internally consistent, so no additional category normalization was required.

---

### 4. Numeric Validity Checks

Numeric fields were checked for invalid negative values where negative quantities or costs would not make business sense.

No records violating the tested non-negative constraints were identified.

Rather than applying unnecessary transformations, the original valid values were retained.

---

## Feature Engineering

After data-quality validation, additional business-oriented features were created.

### Inventory Coverage Ratio

```text
Inventory Coverage Ratio =
Stock Levels / Order Quantities
```

This measures the amount of available inventory relative to the order quantity represented by each record.

It is used as a comparative metric rather than as an automatic classification of overstock or understock, since no business-defined inventory threshold is available.

Additional derived metrics were created to make the processed dataset more useful for downstream analysis.

---

## Power BI Dashboard

The processed dataset stored in Azure Blob Storage was connected to Power BI to create an interactive supply chain performance dashboard.

### Executive Metrics

The dashboard includes headline metrics such as:

- Total Revenue
- Total Stock
- Average Defect Rate
- Average Manufacturing Cost
- Average Transportation Cost

### Dashboard Analysis

The dashboard analyzes several dimensions of supply chain performance.

#### Revenue by Product Type

Compares revenue generation across product categories.

**Business question:**

> Which product categories contribute the most revenue?

---

#### Inventory Coverage by Product Type

Compares inventory coverage ratios across product categories.

**Business question:**

> Which product categories hold relatively more or less inventory compared with their order quantities?

---

#### Manufacturing Cost vs. Defect Rate

A scatter plot compares manufacturing costs with observed defect rates.

**Business question:**

> Is there an observable relationship between manufacturing spending and product defect rates?

The visualization is used to investigate association rather than establish causality.

---

#### Transportation Cost by Mode

Compares average transportation costs across:

- Air
- Road
- Rail
- Sea

**Business question:**

> How does transportation cost differ across logistics modes?

---

## Interactive Analysis

The dashboard includes slicers for:

- Product Type
- Supplier
- Location
- Transportation Mode

These allow stakeholders to move from an overall view of the supply chain to specific operational segments.

For example:

```text
Overall Supply Chain
        │
        ▼
Select Supplier
        │
        ▼
Observe Defect Rate
        │
        ▼
Compare Manufacturing Cost
        │
        ▼
Examine Product / Location / Transportation Mix
```

This supports an exploratory workflow where stakeholders can identify an unusual pattern and investigate the subset of records associated with it.

---

## Dashboard

 Final Power BI dashboard screenshot:

<img width="1266" height="700" alt="image" src="https://github.com/user-attachments/assets/68f56121-658e-4be6-b412-89887a931d48" />


## Azure Data Factory Pipeline

The Azure Data Factory pipeline orchestrates transformation of the raw supply chain dataset.

```text
Azure Blob Storage
        │
        ▼
     sourceRaw
        │
        ▼
     cleanData
        │
        ▼
 businessFeatures
        │
        ▼
  sinkProcessed
        │
        ▼
Azure Blob Storage
```

Azure Data Factory screenshot here:

<img width="959" height="298" alt="image" src="https://github.com/user-attachments/assets/bd5fad33-e3b0-4bbc-a366-db7e4c08b516" />




## Repository Structure

```text
azure-supply-chain-analytics/
│
├── README.md
│
├── data/
│   ├── raw/
│   │   └── supply_chain_data.csv
│   └── processed/
│       └── supply_chain_processed.csv
│
├── azure/
│   ├── pipeline_screenshot.png
│   ├── dataflow_screenshot.png
│   └── architecture.png
│
├── powerbi/
│   └── supply_chain_dashboard.pbix
│
├── images/
│   └── dashboard.png
│
└── docs/
    └── data_dictionary.md
```

---

## Key Takeaways

This project demonstrates how a relatively small dataset can still be used to understand the architecture of a cloud analytics workflow.

The main focus was on building the complete data lifecycle:

```text
Raw Data
   ↓
Cloud Storage
   ↓
Data Pipeline
   ↓
Data Quality
   ↓
Transformation
   ↓
Feature Engineering
   ↓
Business Intelligence
   ↓
Decision Support
```

The project reinforced several important analytics principles:

1. Data quality should be validated before building business metrics.
2. Raw and processed data should be maintained separately.
3. Business metrics need clear interpretations rather than arbitrary thresholds.
4. Dashboard visuals should answer specific business questions.
5. Interactive filtering allows stakeholders to move from high-level performance to specific operational segments.
6. A dashboard is most useful when it supports investigation and decision-making rather than simply displaying metrics.

---

## Future Improvements

Possible extensions to the project include:

- Automating ingestion of new supply chain files
- Adding historical data for trend analysis
- Defining business targets for inventory, defects, and lead times
- Building supplier performance scorecards
- Performing statistical analysis of manufacturing cost and defect rates
- Adding demand forecasting
- Migrating larger workloads to Azure Data Lake or other scalable analytical architectures
- Adding automated pipeline scheduling and monitoring

---

## Security

No Azure credentials, storage account keys, connection strings, SAS tokens, or other secrets are included in this repository.

Azure screenshots are sanitized before publication to avoid exposing account information or credentials.

---

## Author

**Shreyash Agrawal**

MS Business Analytics – Data Science  
The University of Texas at Dallas

Areas of interest: Data Analytics, Data Science, Business Intelligence, Cloud Analytics, Supply Chain Analytics

---

## Disclaimer

This project is intended as a portfolio and learning project. Business interpretations are based on the available dataset and should not be treated as production supply chain recommendations without additional operational context, historical data, and business-defined performance targets.
