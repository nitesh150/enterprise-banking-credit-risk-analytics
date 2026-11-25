# Enterprise Banking – Table Dictionary

## 1. Overview

This document describes the structure and business purpose of the three transactional fact tables used in the Enterprise Banking Credit Risk & Portfolio Intelligence Platform.

The model uses a dimensional architecture with shared dimension tables.

---

## 2. Fact_Loan

**Purpose:** Stores loan-level information, including disbursement, outstanding balances, repayment risk indicators and loan status.

**Candidate Primary Key:** `Loan_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Loan_ID | Candidate PK | Unique loan identifier |
| Customer_ID | FK | Customer reference |
| Product_ID | FK | Loan product reference |
| Branch_ID | FK | Branch reference |
| Disbursement_Date | Date FK | Loan disbursement date |
| Maturity_Date | Attribute | Loan maturity date |
| Disbursed_Amount | Measure | Original loan amount disbursed |
| Outstanding_Amount | Measure | Current outstanding loan balance |
| Interest_Rate | Attribute | Applicable loan interest rate |
| Tenure_Months | Attribute | Loan tenure in months |
| Loan_Status | Attribute | Current loan status |
| DPD | Measure / Attribute | Days Past Due |
| Collateral_Flag | Attribute | Indicates collateral availability |
| DPD Bucket | Derived Attribute | Categorized delinquency bucket |
| DPD Sort | Sorting Attribute | Numeric sorting order for DPD buckets |

**Confirmed Dimension Relationships:**
- Customer_ID → Dim_Customer
- Product_ID → Dim_Product
- Branch_ID → Dim_Branch
- Disbursement_Date → Dim_Calendar

---

## 3. Fact_Payment

**Purpose:** Stores payment transaction information, including payment due amounts, actual payments, payment dates and payment channels.

**Candidate Primary Key:** `Payment_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Payment_ID | Candidate PK | Payment transaction identifier |
| Loan_ID | Business Reference | Related loan identifier |
| Due_Date | Inactive Date FK | Scheduled payment due date |
| Payment_Date | Active Date FK | Actual payment date |
| Due_Amount | Measure | Amount scheduled for payment |
| Paid_Amount | Measure | Amount actually paid |
| Payment_Channel | Attribute | Channel used for payment |
| Payment_Status | Attribute | Payment transaction status |
| Customer_ID | FK | Customer reference |
| Product_ID | FK | Loan product reference |
| Branch_ID | FK | Branch reference |

**Confirmed Dimension Relationships:**
- Customer_ID → Dim_Customer
- Product_ID → Dim_Product
- Branch_ID → Dim_Branch
- Payment_Date → Dim_Calendar (Active)
- Due_Date → Dim_Calendar (Inactive)

---

## 4. Fact_Collections

**Purpose:** Stores collection and recovery activities, including assigned accounts, overdue amounts, recovered amounts and collection outcomes.

**Candidate Primary Key:** `Collection_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Collection_ID | Candidate PK | Collection activity identifier |
| Loan_ID | Business Reference | Related loan identifier |
| Payment_ID | Business Reference | Related payment identifier |
| Assigned_Date | Active Date FK | Date account was assigned for collection |
| Resolution_Date | Attribute | Date collection activity was resolved |
| Collector_ID | Attribute | Assigned collection officer identifier |
| Overdue_Amount | Measure | Amount overdue for collection |
| Recovered_Amount | Measure | Amount recovered |
| Collection_Status | Attribute | Collection activity status |
| Customer_ID | FK | Customer reference |
| Product_ID | FK | Loan product reference |
| Branch_ID | FK | Branch reference |

**Confirmed Dimension Relationships:**
- Customer_ID → Dim_Customer
- Product_ID → Dim_Product
- Branch_ID → Dim_Branch
- Assigned_Date → Dim_Calendar

---

## 5. Relationship and Key Notes

The following dimension relationships have been confirmed from the Power BI relationship view:

| Fact Table | Dimension | Relationship |
|---|---|---|
| Fact_Loan | Dim_Customer | Many-to-One |
| Fact_Loan | Dim_Product | Many-to-One |
| Fact_Loan | Dim_Branch | Many-to-One |
| Fact_Loan | Dim_Calendar | Many-to-One |
| Fact_Payment | Dim_Customer | Many-to-One |
| Fact_Payment | Dim_Product | Many-to-One |
| Fact_Payment | Dim_Branch | Many-to-One |
| Fact_Payment | Dim_Calendar | Many-to-One |
| Fact_Collections | Dim_Customer | Many-to-One |
| Fact_Collections | Dim_Product | Many-to-One |
| Fact_Collections | Dim_Branch | Many-to-One |
| Fact_Collections | Dim_Calendar | Many-to-One |

Note: Candidate primary keys must be validated for uniqueness. Foreign-key designations above refer to confirmed dimension relationships. Loan_ID and Payment_ID in other fact tables are business references; no direct relationships between the fact tables were confirmed in the supplied model view.

## 6. Data Types

Exact Power BI column data types will be documented separately after metadata verification.

## 7. Data Privacy

All datasets used in this portfolio project are synthetic.

No confidential employer, banking customer or production data is included.

---

## 8. Dim_Branch

**Purpose:** Stores branch-level information used for regional performance, branch comparisons and target monitoring.

**Candidate Primary Key:** `Branch_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Branch_ID | Candidate PK | Unique branch identifier |
| Branch_Name | Attribute | Branch name |
| City | Attribute | Branch city |
| Region | Attribute | Geographical region |
| Country | Attribute | Country |
| Branch_Manager | Attribute | Branch manager identifier |
| Branch_Target_Annual | Measure / Attribute | Annual branch performance target |

---

## 9. Dim_Calendar

**Purpose:** Provides a centralized date dimension for time-based analysis, monthly trends, yearly comparisons and period-based reporting.

**Candidate Primary Key:** `Date`

| Column Name | Key / Role | Description |
|---|---|---|
| DimYear | Attribute | Calendar year |
| DimMonthYear | Attribute | Month and year display |
| DimMonth | Attribute | Month name |
| KeyDate | Sorting Attribute | Date-based sorting key |
| MonthNumber | Sorting Attribute | Numeric month sequence |
| Date | Candidate PK | Calendar date |

**Model Usage:**
- Connected to Fact_Loan through Disbursement_Date.
- Connected to Fact_Collections through Assigned_Date.
- Connected to Fact_Payment through Payment_Date (active).
- Fact_Payment Due_Date relationship is inactive.

---

## 10. Dim_Customer

**Purpose:** Stores customer demographic, employment, income and relationship information for customer segmentation and portfolio analysis.

**Candidate Primary Key:** `Customer_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Customer_ID | Candidate PK | Unique customer identifier |
| Customer_Name | Attribute | Customer name |
| Gender | Attribute | Customer gender category |
| Age | Attribute | Customer age |
| Age_Band | Attribute | Customer age classification |
| Annual_Income | Attribute | Annual customer income |
| Employment_Type | Attribute | Employment category |
| City | Attribute | Customer city |
| Customer_Since | Attribute | Customer relationship start date |
| Branch_ID | FK | Associated branch identifier |

---

## 11. Dim_Product

**Purpose:** Stores loan product information used for product-level analysis, portfolio segmentation and interest-rate comparisons.

**Candidate Primary Key:** `Product_ID`

| Column Name | Key / Role | Description |
|---|---|---|
| Product_ID | Candidate PK | Unique product identifier |
| Product_Name | Attribute | Loan product name |
| Base_Interest_Rate | Attribute | Standard product interest rate |
| Max_Tenure_Months | Attribute | Maximum product tenure in months |

---

## 12. Dimension Table Relationships

| Dimension | Primary Key Candidate | Connected Fact Tables |
|---|---|---|
| Dim_Branch | Branch_ID | Fact_Loan, Fact_Payment, Fact_Collections |
| Dim_Calendar | Date | Fact_Loan, Fact_Payment, Fact_Collections |
| Dim_Customer | Customer_ID | Fact_Loan, Fact_Payment, Fact_Collections |
| Dim_Product | Product_ID | Fact_Loan, Fact_Payment, Fact_Collections |

**Note:** Candidate primary keys should be validated for uniqueness. The relationships listed above reflect the supplied Power BI model.


