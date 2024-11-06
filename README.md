## Maven Market project
### Sales of fictive multi-national grocery chain 
Through the investigation the company can get a clear picture about it’s performance overall and by uniqe stores as well. Revenue trend can help to make predictions for the following period. Further inspections and actions can be made with following the monthly performance comparing to the previous month. Revenue vs. target comparison and checking of return rate by product brand can show the areas where improvement can be necessary.

![Maven Market_dashboard](https://github.com/bujdosox/LEGO_project_PowerBI/assets/173184545/2dc07f3c-e332-40ca-ac3a-48ddb6241a59)

![Maven Market_map](https://github.com/bujdosox/LEGO_project_PowerBI/assets/173184545/ff8eb0ea-5f2b-4441-96bf-ecfc281ea371)

### Goal
-   Looking for trends by temporal distribution of revenue
-   Inspection of brands distribution based on: total transactions, total profit, profit margin and return rate 
-   Inspection of monthly data comparing to previous month: transactions, profit and returns
-   Checking revenue vs. target
### Outcome
-   Weekly revenue can be considered constant in the investigation period in the USA and in Mexico. This value is qiute rhapsodic in Canada with wide amplitudes.
-   Return rate is extremly high in Canada (11,14%) despite few transactions and number of stores
-   The USA and Canada did not reach the revenue target in the investigation period (nearly 10% below the target)
-   Significant increase compared to previous month in Guadalajara (MEX) - December 1998: transactions +63,81 %, profit +65,04 % 
### Methods and steps
-   Loading multiple CSV files into Power BI Desktop, 2 of them (Transactions_1997 and Transactions_1198) from separate folder via folder path
-   Checking coloumn quality, coloumn distribution, coloumn profile and data types regarding the whole data set in Power Query editor
-   Transforming data in Power Query editor in order to prepare analysis
-   Creating data model in Model view

        o   connecting data/fact tables (Transactions, Return data) to dimension/lookup tables based on common ID’s and date
        o   Date primary key in Calendar table can connect to stock_date foreign key and transaction_date foreign key in Transactions table. The latter one will be active and the previous one will be inactive relationship.
        o   organizing dimension tables above fact tables in order to vizualize filter flow from up to down and highliht the snowflake scema
        o   Setting one-to-many cardinality and one-way filters
        o   Connecting data tables to each other via shared dimension tables
        o   Hiding all foreign keys from Report view prevent to use them for filtering

![Maven Market_model](https://github.com/bujdosox/LEGO_project_PowerBI/assets/173184545/51049ee6-809b-4246-b5e2-11bd44b76535)

-   Create a separate table for measures in Table view + create DAX measures

        o       Total Transactions = COUNTROWS(Transactions) 
        o       Total Returns = COUNTROWS(Return_Data) 
        o       Total Revenue = SUMX('Transactions', Transactions[quantity] * RELATED('Products'[product_retail_price]))
        o       Total Cost = SUMX('Transactions', Transactions[quantity] * RELATED('Products'[product_cost]))
        o       Total Profit = [Total Revenue] - [Total Cost]
        o       Profit Margin = DIVIDE([Total Profit], [Total Revenue])
        o       Total Profit: Total Revenue - Total Cost
        o       Profit Margin: Total Profit / Total Revenue
        o       Quantity Sold = SUM(Transactions[quantity]) 
        o       Quantity Returned = SUM(Return_Data[quantity]) 
        o       Return Rate = DIVIDE([Quantity Returned], [Quantity Sold])
        o       Last Month Transactions = CALCULATE([Total Transactions], DATEADD('Calendar'[date], -1, MONTH))
        o       Last Month Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, MONTH))
        o       Last Month Profit = CALCULATE([Total Profit], DATEADD('Calendar'[date], -1, MONTH))
        o       Last Month Retruns = CALCULATE([Total Returns], DATEADD('Calendar'[date], -1, MONTH))
        o       Revenue Target = [Last Month Revenue] * 1.05
        o       All Transactions = CALCULATE([Total Transactions], ALL(Transactions))
        o       All Returns = CALCULATE([Total Returns], ALL(Return_Data))
        o       Weekend Transactions = CALCULATE([Total Transactions], 'Calendar'[Weekend] = "Y") 
        o       % Weekend Transactions = DIVIDE([Weekend Transactions], [Total Transactions])
        o       YTD Revenue = CALCULATE([Total Revenue], DATESYTD('Calendar'[date]))
        o       60-Day Revenue = CALCULATE([Total Revenue], DATESINPERIOD('Calendar'[date], MAX('Calendar'[date]), -60, DAY))
        o       Unique Products = DISTINCTCOUNT(Products[product_name])
-   Visualize data in Report view

        o   KPI: Current Month Transactions/Profit/Return
        o   Coloumn chart: Weekly Revenue Trend by temporal distribution
        o   Matrix: Total Transactions, Total Profit, Profit Margin, Return Rate by Product brand
        o   Tree map: Transactions by Region
        o   Gauge: Revenue vs. Target
        o   Map visualization on separate page: highlighting Total Transactions by bubble size
        o   Checking interactions: turn out filtering of any charts/matrixes to the tree map
        o   Summarize outcomes on a separate page
        o   Create a fix panel with buttons: change between pages + clear all filters
        o   Add a new page with map visualization to show transactions by cities + add slicers by countries
