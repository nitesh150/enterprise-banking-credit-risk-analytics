
# Data Model – Relationship Documentation

## 1. Architecture

The project uses a dimensional data model with
three fact tables and four shared dimension tables.

## 2. Fact-to-Dimension Relationships

### Fact_Loan
- Branch_ID → Dim_Branch[Branch_ID]
- Customer_ID → Dim_Customer[Customer_ID]
- Product_ID → Dim_Product[Product_ID]
- Disbursement_Date → Dim_Calendar[Date]

### Fact_Collections
- Branch_ID → Dim_Branch[Branch_ID]
- Customer_ID → Dim_Customer[Customer_ID]
- Product_ID → Dim_Product[Product_ID]
- Assigned_Date → Dim_Calendar[Date]

### Fact_Payment
- Branch_ID → Dim_Branch[Branch_ID]
- Customer_ID → Dim_Customer[Customer_ID]
- Product_ID → Dim_Product[Product_ID]
- Payment_Date → Dim_Calendar[Date]
- Due_Date → Dim_Calendar[Date] (Inactive)

## 3. Relationship Properties

- Cardinality: Many-to-One (*:1)
- Cross-filter direction: Single
- Dimension tables provide filtering context.
- Fact tables store transactional information.

## 4. Date Relationship Handling

The Payment_Date relationship is active.

The Due_Date relationship is inactive.

The inactive relationship can be activated
inside specific DAX calculations using USERELATIONSHIP().

## 5. Security Architecture

SecurityAccess is maintained as a supporting
table for dynamic Row-Level Security (RLS).

## 6. Modeling Benefits

- Consistent filtering across fact tables
- Reusable dimensions
- Simplified DAX calculations
- Improved reporting scalability
- Support for multiple business date perspectives
