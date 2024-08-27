# Telecom Customer Churn Analysis - SQL, Power BI, Python

## Table of Contents
1. 

## Project Objective
Create an entire ETL process in a database and a Power BI dashboard to utilize the Customer Data and achieve below goals:                                 
1. Analyze Customer Data at below levels: 
     a. Demographic
     b. Geographic
     c. Payment and Account Info
     d. Services
2. Study Churner Profile and identify areas for implementing marketing campaigns.
3. Identify a method to predict future churners.

## ETL Framework
Our framework uses below components:
1. CSV file - This is our source file
2. SQL Server Management Studio (SSMS) - We will use its inbuilt import wizard to transform and load the data
3. SQL Server Database - This is where our final data will be loaded and host our data warehouse, tables and views for final usage.

## Dataset Description
No. of rows=6419, No. of columns=32. The dataset consists of customer information of a telecom company like Customer_ID, Gender, Age, Married, State, Number_of_Referrals, Tenure_in_Months, Value_Deal, Phone_Service, Multiple_Lines, Internet_Service, Internet_Type, Online_Security, Online_Backup, Device_Protection_Plan, Premium_Support, Streaming_TV, Streaming_Movies, Streaming_Music, Unlimited_Data, Contract, Paperless_Billing, Payment_Method, Monthly_Charge, Total_Charges, Total_Refunds, Total_Extra_Data_Charges, Total_Long_Distance_Charges, Total_Revenue, Customer_Status, Churn_Category, Churn_Reason.

## Preparing Data for Analysis
### SSMS
1. Created a new database `db_Churn` in SQL Server Management Studio, after connecting to server.
2. Imported our CSV file using `Import Flat File` in SSMS and modified the columns by checking Allow Nulls checkbox for every column except for Candidate_ID (Primary Key), and changing bit datatype to varchar50 datatype to avoid errors on importing.
3. After importing, performed data analysis using SQL queries.
4. Created a new table `prod_Churn` from our previous table `stg_Churn` by replacing NULL values by 'None' or 'No' values.
5. Created two views `vw_ChurnData' and 'vw_JoinData` to be used later in predictive analysis.
   ### Note: For Steps 3, 4 and 5, please refer to `SQL Queries` file above for all the queries used in data analysis.
### Power BI
6. Connected Power BI to our SQL Server and imported `prod_Churn` table into our Power Query Editor using Transform Data option.
7. Created a custom column `Churn Status` using DAX formula and changed the datatype to Whole Number.
   ```bash
   = if [Customer_Status] = "Churned" then 1 else 0
   ```
8. Created a new custom column `Monthly Charge Status` using DAX formula
   ```bash
   = if [Monthly_Charge] < 20 then "<20"
     else if [Monthly_Charge] < 50 then "20-50"
     else if [Monthly_Charge] < 100 then "50-100"
     else ">100"
   ```
