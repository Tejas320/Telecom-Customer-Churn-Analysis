# Telecom Customer Churn Analysis

## Tools Used - SQL, Power BI, Python

## Table of Contents
1. [Project Objective](#project-objective)
2. [ETL Framework](#etl-framework)
3. [Dataset Description](#dataset-description)
4. [Project Workflow](#project-workflow)
5. [Power BI Dashboard](#power-bi-dashboard)
6. [Predictions](#predictions)

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

### Note: Please refer `Dataset` folder above for dataset.

## Project Workflow
### SQL (SSMS)
1. Created a new database `db_Churn` in SQL Server Management Studio, after connecting to server.
2. Imported our CSV file using `Import Flat File` in SSMS and modified the columns by checking Allow Nulls checkbox for every column except for Candidate_ID (Primary Key), and changing bit datatype to varchar50 datatype to avoid errors on importing.
3. After importing, performed data analysis using SQL queries.
4. Created a new table `prod_Churn` from our previous table `stg_Churn` by replacing NULL values by 'None' or 'No' values.
5. Created two views `vw_ChurnData` and `vw_JoinData` to be used later in predictive analysis.
   ### Note: For Steps 3, 4 and 5, please refer to `SQL Queries` file above for all the queries used in data analysis.
### Power BI
6. Connected Power BI to our SQL Server and imported `prod_Churn` table into our Power Query Editor using Transform Data option.
7. Created a new custom column `Churn_Status` and changed the datatype to Whole Number.
   ```bash
   = if [Customer_Status] = "Churned" then 1 else 0
   ```
8. Created a new custom column `Monthly_Charge_Status`.
   ```bash
   = if [Monthly_Charge] < 20 then "<20"
     else if [Monthly_Charge] < 50 then "20-50"
     else if [Monthly_Charge] < 100 then "50-100"
     else ">100"
   ```
9. Created a new table `mapping_AgeGrp` by referencing the `prod_Churn` table and removed all other columns except Age column and removed duplicates from Age column.
10. Created a new custom column `Age_Group` in `mapping_AgeGrp` table.
    ```bash
    = if [Age] < 20 then "<20"
      else if [Age] < 35 then "20-35"
      else if [Age] < 50 then "35-50"
      else ">50"
    ```
11. Created a new custom column `AgeGrpSorting` in `mapping_AgeGrp` table and changed datatype to Whole Number.
    ```bash
    = if [Age_Group] = "<20" then 1
      else if [Age_Group] = "20-35" then 2
      else if [Age_Group] = "35-50" then 3
      else 4
    ```
12. Created a new table `mapping_TenureGrp` by referncing the `prod_Churn` table and removed all other columns except Tenure_in_Months column and removed duplicates.
13. Created a new custom column `Tenure_Group` in `mapping_TenureGrp` table.
    ```bash
    = if [Tenure_in_Months] < 6 then "< 6 Months"
      else if [Tenure_in_Months] < 12 then "6-12 Months"
      else if [Tenure_in_Months] < 18 then "12-18 Months"
      else if [Tenure_in_Months] < 24 then "18-24 Months"
      else ">= 24 Months"
    ```
14. Created a new custom column `TenureGrpSorting` in 'mapping_TenureGrp` table and changed the datatype to Whole Number.
    ```bash
     = if [Tenure_Group] = "< 6 Months" then 1
      else if [Tenure_Group] = "6-12 Months" then 2
      else if [Tenure_Group] = "12-18 Months" then 3
      else if [Tenure_Group] = "18-24 Months" then 4
      else 5
    ```
15. Created a new table `prod_Services` by referencing `prod_Churn` table and unpivoted the columns -  Phone_Service, Multiple_Lines, Internet_Service, Online_Security, Online_Backup, Device_Protection_Plan, Premium_Support, Streaming_TV, Streaming_Movies, Streaming_Music, Unlimited_Data, Paperless_Billing.
16. After transforming the data, data is loaded into the Power BI Desktop.
17. Created a new table `tbl_Measures` to store all the measures we will create.
18. Created a new measure `Total Customers` using DAX formula.
    ```bash
    Total Customers = COUNT(prod_Churn[Customer_ID])
    ```
19. Created a new measure `New Joiners` using DAX formula.
    ```bash
    New Joiners = CALCULATE(COUNT(prod_Churn[Customer_ID]), prod_Churn[Customer_Status] = "Joined")
    ```
20. Created a new measure `Total Churn` using DAX formula.
    ```bash
    Total Churn = SUM(prod_Churn[Churn_Status])
    ```
21. Created a new measure `Churn Rate` using DAX formula.
    ```bash
    Churn Rate = [Total Churn] / [Total Customers]
    ```
22. Created a new measure `Percent of Row Total` using DAX formula.
    ```bash
    Percent of Row Total = 
     DIVIDE(
         SUM('prod_Services'[Churn_Status]),
     CALCULATE(
        SUM('prod_Services'[Churn_Status]),
     ALLEXCEPT('prod_Services', 'prod_Services'[Services])
      )
    )
    ```
23. Created a Power BI report `Churn Analysis - Summary` using various visualizations.
    ### Note: Please refer `Reports` folder above for reports pdf.
### Python (Jupyter Notebook)
23. Connected SQL database to Jupyter Notebook.
24. Performed predictive analytics using machine learning algorithm `Random Forest`.
25. Trained our model on `vw_ChurnData` view created earlier on Step 5 and achieved an accuracy of 84%.
26. Tested our model on `vw_JoinData` view created earlier on Step 5.
27. Exporting the `Predictions` table into the SQL database using `to_sql`.
    ### Note: Please refer `Jupyter Notebook` folder for detailed machine learning code.
## Power BI
28. Imported the `Predictions` table from SQL Server to Power BI.
29. Created a new measure `Count Predicted Churner` using DAX formula.
    ```bash
    Count Predicted Churner = COUNT(Predictions[Customer_ID]) + 0
    ```
30. Created a new measure `Title Predicted Churner` using DAX formula.
    ```bash
    Title Predicted Churner = "COUNT OF PREDICTED CHURNERS : " & COUNT(Predictions[Customer_ID])
    ```
31. Created a Power BI report `Churn Analysis - Prediction` using various visualizations.
    ### Note: Please refer `Reports` folder for reports pdf.

## Power BI Dashboard
![image](https://github.com/user-attachments/assets/512ecf91-c0c7-42bc-bfec-9505871c0414)
#### Churn Analysis - Summary
![image](https://github.com/user-attachments/assets/29cad6bf-bbf3-407f-abc5-94b09de64855)
#### Chain Analysis - Prediction

## Predictions
After training our model, these were the predictions our model made:
1. Count of people who will churn in future (Predicted Churners) was found to be 378.
2. Out of 378 predicted churners, 246 were females and 132 were males.
3. Maximum number (135) of predicted churners were of the age group greater than 50.
4. Maximum number (193) of predicted churners were unmarried.
5. Maximum number (106) of predicted churners were those who were having their tenure group equal to or greater than 2 years.
6. Maximum number (192) of predicted churners were those who were using credit card services.
7. Maximum number (355) of predicted churners were those who were having month-to-month contract.
8. Maximum number (44) of predicted churners were from Uttar Pradesh, followed by Maharashtra(40), Tamil Nadu(37), Karnataka(29), and Andhra Pradesh(24).
