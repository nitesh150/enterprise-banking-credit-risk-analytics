# Enterprise Banking – Data Model

## 1. Overview

This section documents the data architecture and dimensional data model used in the Enterprise Banking Credit Risk & Portfolio Intelligence Platform.

The project follows a Star Schema approach with shared dimension tables to support scalable reporting, analytics, and business intelligence.

## 2. Data Model Components

### Fact Tables

- Fact_Loan
- Fact_Payment
- Fact_Collections

### Dimension Tables

- Dim_Customer
- Dim_Branch
- Dim_Product
- Dim_Calendar

### Supporting Tables

- A_Measure
- Portfolio_Performance
- Customer_Risk_Segment
- Security_Access

## 3. Modeling Approach

- Star Schema architecture
- Fact and dimension separation
- Many-to-one relationships
- Shared dimension tables
- Centralized calendar dimension
- Active and inactive date relationships
- Reusable DAX measures
- Supporting tables for portfolio analysis and security

## 4. Power BI Implementation

The data model supports five report pages:

- Executive Overview
- Credit Risk Analysis
- Customer Portfolio Analytics
- Branch & Regional Performance
- Collections & Recovery Analytics

## 5. Security Architecture

Dynamic Row-Level Security (RLS) is implemented using the Security_Access table.

The security structure supports user-based access restrictions using email, access level, region, and branch information.

## 6. Data Governance

- All datasets are synthetic.
- No confidential employer or customer information is used.
- Shared dimensions provide consistent analytical filtering.
- Centralized measures support reusable business calculations.

## 7. Technical Documentation

Detailed technical documentation:

- [Table Relationships](Table_Relationships.md)
- [Table Dictionary](Table_Dictionary.md)
- [Data Model Diagram](Data_Model_Diagram.png)





