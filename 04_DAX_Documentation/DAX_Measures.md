# DAX Measures Documentation

## Overview

This document catalogs **97 DAX measures** from the supplied Power BI measure export. Measures are grouped by analytical purpose. The expressions below preserve the supplied DAX definitions.

> **Model note:** The formulas reference the model tables and columns shown in the supplied export, including `Fact_Loan`, `Fact_Payment`, `Fact_Collections`, `Dim_Customer`, `Dim_Branch`, `Dim_Product`, and `Dim_Calendar`.

## Measure Categories

### Core Portfolio KPIs

#### `Total Disbursed`

```dax
SUM(Fact_Loan[Disbursed_Amount])
```

#### `Total Loans`

```dax
DISTINCTCOUNT(Fact_Loan[Loan_ID])
```

#### `Total Outstanding`

```dax
SUM(Fact_Loan[Outstanding_Amount])
```

#### `Average Loan Amount`

```dax
DIVIDE([Total Disbursed], [Total Loans], 0)
```

#### `Average DPD`

```dax
AVERAGE(Fact_Loan[DPD])
```

#### `Customers with Loans`

```dax
DISTINCTCOUNT(Fact_Loan[Customer_ID])
```

#### `Customer Count`

```dax
DISTINCTCOUNT(Fact_Loan[Customer_ID])
```

#### `Total Customers`

```dax
DISTINCTCOUNT(Dim_Customer[Customer_ID])
```

#### `Total Branches`

```dax
DISTINCTCOUNT(Dim_Branch[Branch_ID])
```

#### `Average Loans per Branch`

```dax
DIVIDE(
    [Total Loans],
    [Total Branches],
    0
)
```

#### `Risk Exposure (Cr)`

```dax
var _val = 
DIVIDE(
    SUM(Fact_Loan[Outstanding_Amount]),
    10000000
)

RETURN
IF(ISBLANK(_val),"NA", _val)
```

#### `As of today's date`

```dax
TODAY()
```

### Credit Risk, NPA & Delinquency

#### `NPA Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[Loan_Status] = "NPA"
        )
```

#### `NPA Loan Rate`

```dax
DIVIDE(
            [NPA Loans],
            [Total Loans],
            0
        )
```

#### `NPA Outstanding`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[Loan_Status] = "NPA"
        )
```

#### `NPA Outstanding Ratio`

```dax
DIVIDE(
            [NPA Outstanding],
            [Total Outstanding],
            0
        )
```

#### `Performing Amount`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[Loan_Status] = "Standard"
        )
```

#### `SMA Amount`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[Loan_Status] = "SMA"
        )
```

#### `Non Performing Amount`

```dax
[NPA Outstanding]
```

#### `Portfolio Performance Amount`

```dax
VAR SelectedType =
            SELECTEDVALUE(Portfolio_Performance[Portfolio Type])
        RETURN
            SWITCH(
                SelectedType,
                "Performing", [Performing Amount] + [SMA Amount],
                "Non-Performing", [Non Performing Amount],
                BLANK()
            )
```

#### `Current Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] <= 0
        )
```

#### `DPD 1-30 Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] >= 1,
            Fact_Loan[DPD] <= 30
        )
```

#### `DPD 31-60 Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] >= 31,
            Fact_Loan[DPD] <= 60
        )
```

#### `DPD 61-90 Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] >= 61,
            Fact_Loan[DPD] <= 90
        )
```

#### `DPD Above 90 Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] > 90
        )
```

#### `DPD 1-30 Outstanding`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[DPD] >= 1,
            Fact_Loan[DPD] <= 30
        )
```

#### `DPD 31-60 Outstanding`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[DPD] >= 31,
            Fact_Loan[DPD] <= 60
        )
```

#### `DPD 61-90 Outstanding`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[DPD] >= 61,
            Fact_Loan[DPD] <= 90
        )
```

#### `DPD Above 90 Outstanding`

```dax
CALCULATE(
            [Total Outstanding],
            Fact_Loan[DPD] > 90
        )
```

#### `Delinquent Loans`

```dax
CALCULATE(
            [Total Loans],
            Fact_Loan[DPD] > 0
        )
```

#### `Delinquency Rate`

```dax
DIVIDE(
            [Delinquent Loans],
            [Total Loans],
            0
        )
```

#### `DPD Loan Count`

```dax
DISTINCTCOUNT(Fact_Loan[Loan_ID])
```

#### `DPD Portfolio %`

```dax
DIVIDE(
    [Total Outstanding],
    CALCULATE(
        [Total Outstanding],
        REMOVEFILTERS(Fact_Loan[DPD Bucket])
    ),
    0
)
```

#### `DPD Heatmap Color`

```dax
SWITCH(
    SELECTEDVALUE(Fact_Loan[DPD Bucket]),
    "Current", "#285B55",
    "1-30 Days", "#536C58",
    "31-60 Days", "#8A7545",
    "61-90 Days", "#A9683F",
    "90+ Days", "#A94F57",
    "Grey"
)
```

#### `Overdue Exposure by DPD`

```dax
CALCULATE(
    [Total Outstanding],
    Fact_Loan[DPD] > 0
)

EVALUATE
    SUMMARIZECOLUMNS(
        "Average DPD", [Average DPD],
        "Average Loan Amount", [Average Loan Amount],
        "Collection Efficiency", [Collection Efficiency],
        "Customers with Loans", [Customers with Loans],
        "NPA Loan Rate", [NPA Loan Rate],
        "NPA Loans", [NPA Loans],
        "NPA Outstanding", [NPA Outstanding],
        "NPA Outstanding Ratio", [NPA Outstanding Ratio],
        "Recovery Rate", [Recovery Rate],
        "Total Disbursed", [Total Disbursed],
        "Total Due", [Total Due],
        "Total Loans", [Total Loans],
        "Total Outstanding", [Total Outstanding],
        "Total Overdue", [Total Overdue],
        "Total Paid", [Total Paid],
        "Total Recovered", [Total Recovered],
        "As of today's date", [As of today's date],
        "Avg Disbursed per Customer", [Avg Disbursed per Customer],
        "Avg Outstanding per Customer", [Avg Outstanding per Customer],
        "Repeat Borrowers", [Repeat Borrowers],
        "Repeat Borrower %", [Repeat Borrower %],
        "NPA Customers", [NPA Customers],
        "NPA Customer %", [NPA Customer %],
        "Top 10 Customer Outstanding", [Top 10 Customer Outstanding],
        "Top 10 Customer Concentration %", [Top 10 Customer Concentration %],
        "Customer Outstanding Rank", [Customer Outstanding Rank],
        "Avg Loans per Customer", [Avg Loans per Customer],
        "Average Customer Age", [Average Customer Age],
        "Average Annual Income", [Average Annual Income],
        "Customer Count", [Customer Count],
        "Performing Amount", [Performing Amount],
        "SMA Amount", [SMA Amount],
        "Non Performing Amount", [Non Performing Amount],
        "Portfolio Performance Amount", [Portfolio Performance Amount],
        "Total Customers", [Total Customers],
        "Portfolio Outstanding %", [Portfolio Outstanding %],
        "Current Loans", [Current Loans],
        "DPD 1-30 Loans", [DPD 1-30 Loans],
        "DPD 31-60 Loans", [DPD 31-60 Loans],
        "DPD 61-90 Loans", [DPD 61-90 Loans],
        "DPD Above 90 Loans", [DPD Above 90 Loans],
        "DPD 1-30 Outstanding", [DPD 1-30 Outstanding],
        "DPD 31-60 Outstanding", [DPD 31-60 Outstanding],
        "DPD 61-90 Outstanding", [DPD 61-90 Outstanding],
        "DPD Above 90 Outstanding", [DPD Above 90 Outstanding],
        "Delinquent Loans", [Delinquent Loans],
        "Delinquency Rate", [Delinquency Rate],
        "NPA Product Rank", [NPA Product Rank],
        "NPA Region Rank", [NPA Region Rank],
        "NPA Exposure Contribution %", [NPA Exposure Contribution %],
        "Branch Disbursed Rank", [Branch Disbursed Rank],
        "Branch Outstanding Rank", [Branch Outstanding Rank],
        "Branch NPA Rate", [Branch NPA Rate],
        "Branch NPA Outstanding", [Branch NPA Outstanding],
        "Regional Outstanding", [Regional Outstanding],
        "Regional Disbursed", [Regional Disbursed],
        "Regional NPA Rate", [Regional NPA Rate],
        "Branch Annual Target", [Branch Annual Target],
        "Branch Target Achievement %", [Branch Target Achievement %],
        "Branch Outstanding Contribution %", [Branch Outstanding Contribution %],
        "Branch Disbursement Contribution %", [Branch Disbursement Contribution %],
        "Unrecovered Amount", [Unrecovered Amount],
        "Payment Gap", [Payment Gap],
        "Payment Achievement %", [Payment Achievement %],
        "Total Collection Cases", [Total Collection Cases],
        "Average Overdue per Case", [Average Overdue per Case],
        "Average Recovery per Case", [Average Recovery per Case],
        "Recovery Contribution %", [Recovery Contribution %],
        "Disbursement MTD", [Disbursement MTD],
        "Disbursement QTD", [Disbursement QTD],
        "Disbursement YTD", [Disbursement YTD],
        "Disbursement Previous Month", [Disbursement Previous Month],
        "Disbursement MoM Change", [Disbursement MoM Change],
        "Disbursement MoM %", [Disbursement MoM %],
        "Disbursement Previous Year", [Disbursement Previous Year],
        "Disbursement YoY Change", [Disbursement YoY Change],
        "Disbursement YoY %", [Disbursement YoY %],
        "Paid MTD", [Paid MTD],
        "Paid YTD", [Paid YTD],
        "Recovered MTD", [Recovered MTD],
        "Recovered YTD", [Recovered YTD],
        "Rolling 12M Disbursement", [Rolling 12M Disbursement],
        "Rolling 12M Collections", [Rolling 12M Collections],
        "Risk Exposure (Cr)", [Risk Exposure (Cr)],
        "DPD Heatmap Color", [DPD Heatmap Color],
        "DPD Loan Count", [DPD Loan Count],
        "DPD Portfolio %", [DPD Portfolio %],
        "Non-NPA Customers", [Non-NPA Customers],
        "Customer Risk Count", [Customer Risk Count],
        "Customer Risk Percentage", [Customer Risk Percentage],
        "Total Branches", [Total Branches],
        "Average Loans per Branch", [Average Loans per Branch],
        "Repeat Borrower Rate", [Repeat Borrower Rate],
        "Total Amount Collected", [Total Amount Collected],
        "Pending Collection Amount", [Pending Collection Amount],
        "Amount Due by Due Date", [Amount Due by Due Date],
        "Overdue Exposure by DPD", [Overdue Exposure by DPD]
    )
```

### Customer Analytics

#### `Avg Disbursed per Customer`

```dax
DIVIDE(
            [Total Disbursed],
            [Customers with Loans],
            0
        )
```

#### `Avg Outstanding per Customer`

```dax
DIVIDE(
            [Total Outstanding],
            [Customers with Loans],
            0
        )
```

#### `Repeat Borrowers`

```dax
COUNTROWS(
            FILTER(
                VALUES(Fact_Loan[Customer_ID]),
                CALCULATE([Total Loans]) > 1
            )
        )
```

#### `Repeat Borrower %`

```dax
DIVIDE(
            [Repeat Borrowers],
            [Customers with Loans],
            0
        )
```

#### `Repeat Borrower Rate`

```dax
DIVIDE(
    [Repeat Borrowers],
    [Customers with Loans],
    0
)
```

#### `NPA Customers`

```dax
COUNTROWS(
            FILTER(
                VALUES(Fact_Loan[Customer_ID]),
                CALCULATE([NPA Loans]) > 0
            )
        )
```

#### `NPA Customer %`

```dax
DIVIDE(
            [NPA Customers],
            [Customers with Loans],
            0
        )
```

#### `Non-NPA Customers`

```dax
[Customers with Loans] - [NPA Customers]
```

#### `Top 10 Customer Outstanding`

```dax
VAR TopCustomers =
            TOPN(
                10,
                ADDCOLUMNS(
                    ALLSELECTED(Fact_Loan[Customer_ID]),
                    "@Outstanding", [Total Outstanding]
                ),
                [@Outstanding], DESC
            )
        RETURN
            SUMX(
                TopCustomers,
                [@Outstanding]
            )
```

#### `Top 10 Customer Concentration %`

```dax
DIVIDE(
            [Top 10 Customer Outstanding],
            [Total Outstanding],
            0
        )
```

#### `Customer Outstanding Rank`

```dax
RANKX(
            ALLSELECTED(Fact_Loan[Customer_ID]),
            [Total Outstanding],
            ,
            DESC,
            DENSE
        )
```

#### `Avg Loans per Customer`

```dax
DIVIDE(
            [Total Loans],
            [Customers with Loans],
            0
        )
```

#### `Average Customer Age`

```dax
AVERAGE(Dim_Customer[Age])
```

#### `Average Annual Income`

```dax
AVERAGE(Dim_Customer[Annual_Income])
```

#### `Customer Risk Count`

```dax
VAR SelectedRisk =
    SELECTEDVALUE(
        Customer_Risk_Segment[Risk Segment]
    )
RETURN
    SWITCH(
        SelectedRisk,
        "NPA-Exposed", [NPA Customers],
        "Non-NPA", [Non-NPA Customers],
        BLANK()
    )
```

#### `Customer Risk Percentage`

```dax
DIVIDE(
    [Customer Risk Count],
    [Customers with Loans],
    0
)
```

### Product & Regional Risk Analysis

#### `NPA Product Rank`

```dax
RANKX(
            ALLSELECTED(Dim_Product[Product_Name]),
            [NPA Outstanding],
            ,
            DESC,
            DENSE
        )
```

#### `NPA Region Rank`

```dax
RANKX(
            ALLSELECTED(Dim_Branch[Region]),
            [NPA Outstanding],
            ,
            DESC,
            DENSE
        )
```

#### `NPA Exposure Contribution %`

```dax
DIVIDE(
            [NPA Outstanding],
            CALCULATE(
                [NPA Outstanding],
                ALLSELECTED(Dim_Product),
                ALLSELECTED(Dim_Branch)
            ),
            0
        )
```

#### `Portfolio Outstanding %`

```dax
DIVIDE(
            [Total Outstanding],
            CALCULATE(
                [Total Outstanding],
                ALL(Dim_Product),
                ALL(Dim_Branch)
            ),
            0
        )
```

#### `Regional Outstanding`

```dax
[Total Outstanding]
```

#### `Regional Disbursed`

```dax
[Total Disbursed]
```

#### `Regional NPA Rate`

```dax
[NPA Loan Rate]
```

### Branch Performance

#### `Branch Disbursed Rank`

```dax
RANKX(
            ALLSELECTED(Dim_Branch[Branch_Name]),
            [Total Disbursed],
            ,
            DESC,
            DENSE
        )
```

#### `Branch Outstanding Rank`

```dax
RANKX(
            ALLSELECTED(Dim_Branch[Branch_Name]),
            [Total Outstanding],
            ,
            DESC,
            DENSE
        )
```

#### `Branch NPA Rate`

```dax
[NPA Loan Rate]
```

#### `Branch NPA Outstanding`

```dax
[NPA Outstanding]
```

#### `Branch Annual Target`

```dax
SUM(Dim_Branch[Branch_Target_Annual])
```

#### `Branch Target Achievement %`

```dax
DIVIDE(
            [Total Disbursed],
            [Branch Annual Target],
            0
        )
```

#### `Branch Outstanding Contribution %`

```dax
DIVIDE(
            [Total Outstanding],
            CALCULATE(
                [Total Outstanding],
                ALLSELECTED(Dim_Branch[Branch_Name])
            ),
            0
        )
```

#### `Branch Disbursement Contribution %`

```dax
DIVIDE(
            [Total Disbursed],
            CALCULATE(
                [Total Disbursed],
                ALLSELECTED(Dim_Branch[Branch_Name])
            ),
            0
        )
```

### Collections & Recovery

#### `Total Due`

```dax
SUM(Fact_Payment[Due_Amount])
```

#### `Total Paid`

```dax
SUM(Fact_Payment[Paid_Amount])
```

#### `Total Overdue`

```dax
SUM(Fact_Collections[Overdue_Amount])
```

#### `Total Recovered`

```dax
SUM(Fact_Collections[Recovered_Amount])
```

#### `Collection Efficiency`

```dax
DIVIDE([Total Paid], [Total Due], 0)
```

#### `Recovery Rate`

```dax
DIVIDE(
            [Total Recovered],
            [Total Overdue],
            0
        )
```

#### `Unrecovered Amount`

```dax
[Total Overdue] - [Total Recovered]
```

#### `Payment Gap`

```dax
[Total Due] - [Total Paid]
```

#### `Payment Achievement %`

```dax
DIVIDE(
            [Total Paid],
            [Total Due],
            0
        )
```

#### `Total Collection Cases`

```dax
DISTINCTCOUNT(Fact_Collections[Collection_ID])
```

#### `Average Overdue per Case`

```dax
DIVIDE(
            [Total Overdue],
            [Total Collection Cases],
            0
        )
```

#### `Average Recovery per Case`

```dax
DIVIDE(
            [Total Recovered],
            [Total Collection Cases],
            0
        )
```

#### `Recovery Contribution %`

```dax
DIVIDE(
            [Total Recovered],
            CALCULATE(
                [Total Recovered],
                ALLSELECTED(Dim_Branch)
            ),
            0
        )
```

#### `Total Amount Collected`

```dax
SUM(Fact_Payment[Paid_Amount])
```

#### `Pending Collection Amount`

```dax
MAX(
    0,
    [Total Due] - [Total Amount Collected]
)
```

#### `Amount Due by Due Date`

```dax
CALCULATE(
    [Total Due],
    USERELATIONSHIP(
        Dim_Calendar[Date],
        Fact_Payment[Due_Date]
    )
)
```

### Time Intelligence

#### `Disbursement MTD`

```dax
CALCULATE(
            [Total Disbursed],
            DATESMTD(Dim_Calendar[Date])
        )
```

#### `Disbursement QTD`

```dax
CALCULATE(
            [Total Disbursed],
            DATESQTD(Dim_Calendar[Date])
        )
```

#### `Disbursement YTD`

```dax
CALCULATE(
            [Total Disbursed],
            DATESYTD(Dim_Calendar[Date])
        )
```

#### `Disbursement Previous Month`

```dax
CALCULATE(
            [Total Disbursed],
            DATEADD(Dim_Calendar[Date], -1, MONTH)
        )
```

#### `Disbursement MoM Change`

```dax
[Total Disbursed] - [Disbursement Previous Month]
```

#### `Disbursement MoM %`

```dax
DIVIDE(
            [Disbursement MoM Change],
            [Disbursement Previous Month],
            0
        )
```

#### `Disbursement Previous Year`

```dax
CALCULATE(
            [Total Disbursed],
            SAMEPERIODLASTYEAR(Dim_Calendar[Date])
        )
```

#### `Disbursement YoY Change`

```dax
[Total Disbursed] - [Disbursement Previous Year]
```

#### `Disbursement YoY %`

```dax
DIVIDE(
            [Disbursement YoY Change],
            [Disbursement Previous Year],
            0
        )
```

#### `Paid MTD`

```dax
CALCULATE(
            [Total Paid],
            DATESMTD(Dim_Calendar[Date])
        )
```

#### `Paid YTD`

```dax
CALCULATE(
            [Total Paid],
            DATESYTD(Dim_Calendar[Date])
        )
```

#### `Recovered MTD`

```dax
CALCULATE(
            [Total Recovered],
            DATESMTD(Dim_Calendar[Date])
        )
```

#### `Recovered YTD`

```dax
CALCULATE(
            [Total Recovered],
            DATESYTD(Dim_Calendar[Date])
        )
```

#### `Rolling 12M Disbursement`

```dax
CALCULATE(
            [Total Disbursed],
            DATESINPERIOD(
                Dim_Calendar[Date],
                MAX(Dim_Calendar[Date]),
                -12,
                MONTH
            )
        )
```

#### `Rolling 12M Collections`

```dax
CALCULATE(
            [Total Paid],
            DATESINPERIOD(
                Dim_Calendar[Date],
                MAX(Dim_Calendar[Date]),
                -12,
                MONTH
            )
        )
```

## Notes for Readers

- Measures are evaluated in the current Power BI filter context unless their DAX expression modifies that context.
- Percentage measures should generally be formatted as percentages in Power BI; amount measures should use the currency/unit convention selected for the report.
- Time-intelligence measures depend on `Dim_Calendar[Date]` and the model's date relationships.
- `DPD Heatmap Color` returns hexadecimal color values for conditional formatting.
- `Portfolio Performance Amount` uses the selected value from `Portfolio_Performance[Portfolio Type]`.
- `Customer Risk Count` uses the selected value from `Customer_Risk_Segment[Risk Segment]`.

## Source and Scope

This documentation was prepared from the DAX measure export supplied for this portfolio project. It documents the formulas present in that export; it does not independently validate every measure's business definition, relationship behavior, or visual usage.

The original `.pbix` file is intentionally not included in this public repository.
