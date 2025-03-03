Cohort Analysis Project on Superstore Retail Data

This project involves performing a cohort analysis on a Superstore retail dataset obtained from Kaggle. The goal is to analyze customer behavior over time using various cohort metrics. The dataset contains information about transactions, products, and customers, which is processed and modeled in Power BI.

-------------------------------------------------------
Dataset Attribute Information

InvoiceNo: Invoice number, a 6-digit integral value uniquely identifying each transaction. Invoices starting with 'C' indicate cancellations.
StockCode: Product code, a 5-digit number uniquely identifying each product.
Description: Name of the product.
Quantity: The quantity of each product per transaction.
InvoiceDate: Date and time when the transaction occurred.
UnitPrice: The unit price of each product.
CustomerID: Unique ID assigned to each customer.
Country: Customer's country.
Data Preprocessing Steps
The following data preprocessing steps were implemented to prepare the dataset for analysis:

-----------------------------------------------------------------
Data Type Adjustments:

InvoiceNo: Converted to text.
StockCode: Converted to text.
InvoiceDate: Converted to date format (MM/DD/YYYY).
CustomerID: Converted to a whole number.
Handling Missing Values:
Removed rows where CustomerID is missing.
Removing Canceled Orders:
Transactions with invoices starting with 'C' were excluded.
Sales Calculation:
A new column, Sales, was created by multiplying Quantity and UnitPrice.
Sales entries with a value of 0 were removed.
Additional Tables Created
Two new tables were added to enhance cohort analysis:

DimCustomers:

Contains CustomerID, First Transaction Month, and First Transaction Week.
DimDate:

Contains all unique dates from 12/1/2010 to 12/9/2011.
Includes Start of Month and Start of Week attributes.
Data Modeling in Power BI
The following data modeling steps were implemented in Power BI:

Relationships:
CustomerID from DimCustomers was linked to CustomerID in the main FactSales table.
Date from DimDate was linked to InvoiceDate in FactSales.
DAX Measures
Eighteen DAX measures were created to perform the cohort analysis:

Active Customers:
Active Customers = DISTINCTCOUNT(FactSales[CustomerID])
Churned Customers:
Churned Customers = SWITCH(TRUE(), ISBLANK([Retention Rate]), BLANK(), [New Customers] - [Cohort Performance])
Churned Rate:
Churned Rate = DIVIDE([Churned Customers],[New Customers])
Cohort Performance:
Cohort Performance = VAR _minDate = MIN(DimDate[Start of Month]) VAR _maxDate = MAX(DimDate[Start of Month]) RETURN CALCULATE([Active Customers], REMOVEFILTERS(DimDate[Start of Month]), RELATEDTABLE(DimCustomers), DimCustomers[First Transaction Month] >= _minDate && DimCustomers[First Transaction Month] <= _maxDate)
Lost Customers:
Lost Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE(VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month])) VAR _lostCustomers = EXCEPT(_customersLastMonth, _customersThisMonth) RETURN COUNTROWS(_lostCustomers)
New Customers:
New Customers = CALCULATE([Active Customers], FactSales[Months Since First Transaction] = 0)
Recovered Customers:
Recovered Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE(VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month])) VAR _newCustomers = CALCULATETABLE(VALUES(FactSales[CustomerID]), FactSales[Months Since First Transaction] = 0) VAR _recoveredCustomers = EXCEPT(EXCEPT(_customersThisMonth, _customersLastMonth), _newCustomers) RETURN COUNTROWS(_recoveredCustomers)
Retained Customers:
Retained Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE(VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month])) VAR _retainedCustomers = INTERSECT(_customersLastMonth, _customersThisMonth) RETURN COUNTROWS(_retainedCustomers)
Retention Rate:
Retention Rate = DIVIDE([Cohort Performance],[New Customers])
Weekly Cohort Measures
Active Customers LW:
Active Customers LW = CALCULATE([Active Customers], DATEADD(DimDate[Date], -7, DAY))
Weekly Churned Customers:
Weekly Churned Customers = SWITCH(TRUE(), ISBLANK([Weekly Retention Rate]), BLANK(), [Weekly New Customers] - [Weekly Cohort Performance])
Weekly Churned Rate:
Weekly Churned Rate = DIVIDE([Weekly Churned Customers],[Weekly New Customers])
Weekly Cohort Performance:
Weekly Cohort Performance = VAR _minDate = MIN(DimDate[Start of Week]) VAR _maxDate = MAX(DimDate[Start of Week]) RETURN CALCULATE([Active Customers], REMOVEFILTERS(DimDate[Start of Week]), RELATEDTABLE(DimCustomers), DimCustomers[First Transaction Week] >= _minDate && DimCustomers[First Transaction Week] <= _maxDate)
Weekly Lost Customers:
Weekly Lost Customers = VAR _customersThisWeek = VALUES(FactSales[CustomerID]) VAR _customersLastWeek = CALCULATETABLE(VALUES(FactSales[CustomerID]), DATEADD(DimDate[Date], -7, DAY)) VAR _weeklylostCustomers = EXCEPT(_customersLastWeek, _customersThisWeek) RETURN COUNTROWS(_weeklylostCustomers)
Weekly New Customers:
Weekly New Customers = CALCULATE([Active Customers], FactSales[Weeks Since First Transaction] = 0)
Weekly Recovered Customers:
Weekly Recovered Customers = VAR _customersThisWeek = VALUES(FactSales[CustomerID]) VAR _customersLastWeek = CALCULATETABLE(VALUES(FactSales[CustomerID]), DATEADD(DimDate[Date], -7, DAY)) VAR _newCustomers = CALCULATETABLE(VALUES(FactSales[CustomerID]), FactSales[Weeks Since First Transaction] = 0) VAR _weeklyrecoveredCustomers = EXCEPT(EXCEPT(_customersThisWeek, _customersLastWeek), _newCustomers) RETURN COUNTROWS(_weeklyrecoveredCustomers)
Weekly Retained Customers:
Weekly Retained Customers = VAR _customersThisWeek = VALUES(FactSales[CustomerID]) VAR _customersLastWeek = CALCULATETABLE(VALUES(FactSales[CustomerID]), DATEADD(DimDate[Date], -7, DAY)) VAR _weeklyretainedCustomers = INTERSECT(_customersLastWeek, _customersThisWeek) RETURN COUNTROWS(_weeklyretainedCustomers)
Weekly Retention Rate:
Weekly Retention Rate = DIVIDE([Weekly Cohort Performance],[Weekly New Customers])
