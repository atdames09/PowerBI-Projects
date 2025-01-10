# Maven Toy Store - Sales Dashboard

![image](https://github.com/user-attachments/assets/8f5b37bf-73aa-4b58-b88b-15188cb0bdd8)

## Introduction
Using sample sales data for Maven Toys - a toy store - I tasked myself with building out a Sales Dashboard. The sample data was provided by https://mavenanalytics.io/
## Extract Transform Load
Six .csv files were imported; calendar, data_dictionary, inventory, sales, stores, and products. Then I inspected each data table to identify primary and foreign keys. Using Power Query I cleaned the data by correcting data types/formats, removing null/duplicate data, and creating new columns for filtering or relationships.
## Data Model
![image](https://github.com/user-attachments/assets/bea93008-09d8-49c2-99d0-a0825081381d)

At this step I wanted to create a star schema data model. I identified 3 dimension tables: stores; calendar; and products, and 2 fact tables: sales and inventory. One-to-many relationships were established between the dimension and fact tables. 
## Measures
At this stage measures were created to show data in additional ways. In order to create the KPI cards for revenue, profit, and orders - multiple new measures were made. 

#### Total Revenue
Totals revenue
```DAX
total revenue =
SUM(sales[Revenue])
```

#### Last Month Revenue
Totals revenue for the previous month
```DAX
Last Month Revenue =
CALCULATE(
	SUM(sales[Revenue]),
	PARALLELPERIOD('calendar'[Date], -1, MONTH)
)
```

#### MoM Revenue
Finds the month-month-month amount for revenue
```DAX
MoM Revenue =
IFERROR(
	(SUM(sales[Revenue]) - sales[Last Month Revenue]) / [Last Month Revenue],
	BLANK()
)
```

#### KPI revenueArrow
Creates an up or down arrow to show the MoM direction
```DAX
KPI revenueArrow =
IF(
	[MoM Revenue] >= 0,
IF(
	[MoM Revenue] > 0, UNICHAR(9650),
	UNICHAR(9148)),
	UNICHAR(9660)
)
```

#### KPI revenue change
Combines the KPI arrow with the month-over-month amount into one string.
```DAX
KPI revenue change =
CONCATENATE(
	[KPI revenueArrow],
	FORMAT(
	[MoM Revenue]
	,"0.0%")
)
```

## Dashboard
Four KPI cards were created at the top of the dashboard to allow stakeholders to quickly identify totals and month-over-month change. This information is important and can be digested very quickly, so it is placed where most viewers begin viewing the report. Afterwards, a line chart was created to show change over time. This allows trend analysis across different product categories. Bar charts are being used to show performance across products and store locations. Finally, a table is being used to show monthly order, revenue, and profit totals and change for each store location. While this dashboard is meant for high level information that can be quickly read, the table allows stakeholders to see top or underperforming stores, as well as do deeper analysis.
