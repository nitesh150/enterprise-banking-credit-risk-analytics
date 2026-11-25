
# Enterprise Banking – Data Model

## 1. Overview

This section documents the data architecture and dimensional
data model used in the Enterprise Banking Credit Risk
& Portfolio Intelligence Platform.

The project follows a Star Schema approach to support
scalable reporting, analytics, and business intelligence.

## 2. Data Model Components

### Fact Tables
- Fact_Loan
- Fact_Payment
- Fact_Collections

### Dimension Tables
- Dim_Loan
- Dim_Customer
- Dim_Branch
- Dim_Product
- Dim_Calendar

### Supporting Tables
- A_Measure
- Portfolio_Performance

## 3. Modeling Approach

- Star Schema architecture
- Fact and dimension separation
- Defined table relationships
- Centralized calendar dimension
- Reusable DAX measures
- Optimized analytical reporting

## 4. Power BI Implementation

The data model supports five report pages:
- Executive Overview
- Credit Risk Analysis
- Customer Analytics
- Branch Performance
- Collections & Recovery

## 5. Data Privacy

All datasets used in this project are synthetic. No confidential employer or customer information has been used.


