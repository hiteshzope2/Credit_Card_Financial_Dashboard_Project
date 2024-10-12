# Credit_Card_Financial_Dashboard_Project


## Project Objective
To develop a comprehensive credit card weekly dashboard that provides real-time insights into key performance metrics and trends, enabling stakeholders to monitor and analyze credit card operations effectively.

## Table of Contents
- [Project Objective](#project-objective)
- [Import Data to SQL Database](#import-data-to-sql-database)
- [DAX Queries](#dax-queries)
- [Project Insights - Week 53 (31st Dec)](#project-insights)
- [Dashboard Images](#dashboard-images)

## Import Data to SQL Database

### Steps
1. **Prepare CSV files**:
   - `credit_card.csv`
   - `customer.csv`
   - `cust_add.csv` (for additional customer data to check real-time functionality)
   - `cc_add.csv` (for additional credit card data to check real-time functionality)

2. **Create tables in SQL**:
   ```sql
   CREATE TABLE cc_detail (
          Client_Num INT,
          Card_Category VARCHAR(20),
          Annual_Fees INT,
          Activation_30_Days INT,
          Customer_Acq_Cost INT,
          Week_Start_Date DATE,
          Week_Num VARCHAR(20),
          Qtr VARCHAR(10),
          current_year INT,
          Credit_Limit DECIMAL(10,2),
          Total_Revolving_Bal INT,
          Total_Trans_Amt INT,
          Total_Trans_Ct INT,
          Avg_Utilization_Ratio DECIMAL(10,3),
          Use_Chip VARCHAR(10),
          Exp_Type VARCHAR(50),
          Interest_Earned DECIMAL(10,3),
          Delinquent_Acc VARCHAR(5));


   CREATE TABLE cust_detail (
          Client_Num INT,
          Customer_Age INT,
          Gender VARCHAR(5),
          Dependent_Count INT,
          Education_Level VARCHAR(50),
          Marital_Status VARCHAR(20),
          State_cd VARCHAR(50),
          Zipcode VARCHAR(20),
          Car_Owner VARCHAR(5),
          House_Owner VARCHAR(5),
          Personal_Loan VARCHAR(5),
          Contact VARCHAR(50),
          Customer_Job VARCHAR(50),
          Income INT,
          Cust_Satisfaction_Score INT);
   ```
3. **Import CSV files into SQL**:

   ```sql
      LOAD DATA INFILE  'D:/credit_card.csv'
      into table cc_detail
      FIELDS TERMINATED by ','
      ENCLOSED by '"'
      lines terminated by '\n'
      IGNORE 1 ROWS;

      LOAD DATA INFILE  'D:/customer.csv'
      into table cust_detail
      FIELDS TERMINATED by ','
      ENCLOSED by '"'
      lines terminated by '\n'
      IGNORE 1 ROWS;
   ```

## DAX Queries

### Create AgeGroup Column
```dax
AgeGroup = SWITCH(
     TRUE(),
     'ccdb cust_detail'[Customer_Age] < 30, "20-30",
     'ccdb cust_detail'[Customer_Age] >=30 && 'ccdb cust_detail'[Customer_Age] < 40, "30-40",
     'ccdb cust_detail'[Customer_Age] >=40 && 'ccdb cust_detail'[Customer_Age] < 50, "40-50",
     'ccdb cust_detail'[Customer_Age] >=50 && 'ccdb cust_detail'[Customer_Age] < 60, "50-60",
     'ccdb cust_detail'[Customer_Age] >=60,"60+",
     "unknown")
)
```

### Create IncomeGroup Column
```dax
IncomeGroup = SWITCH(
     TRUE(),
     'ccdb cust_detail'[Income] < 35000, "Low",
     'ccdb cust_detail'[Income] >= 35000 && 'ccdb cust_detail'[Income] < 70000, "Med",
     'ccdb cust_detail'[Income] >= 70000, "High",
     "unkonw")
)
```

### Calculate Revenue
```dax
Revenue = 'ccdb cc_detail'[Annual_Fees]+'ccdb cc_detail'[Total_Trans_Amt]+'ccdb cc_detail'[Interest_Earned]
```

### Calculate Week Number
```dax
WeekNum2 = WEEKNUM('ccdb cc_detail'[Week_Start_Date].[Date] )
```

### Calculate Week On Week Revenue
```dax
wow_revenue = DIVIDE(([Current_week_revenue]-[Previous_week_revenue]), [Previous_week_revenue])
```

### Current Week Revenue
```dax
Current_week_revenue = CALCULATE(
     SUM('ccdb cc_detail'[Revenue]),
     FILTER(
        ALL('ccdb cc_detail'),
        'ccdb cc_detail'[WeekNum2] = MAX('ccdb cc_detail'[WeekNum2])))
```

### Previous Week Revenue
```dax
Previous_week_revenue = CALCULATE(
     SUM('ccdb cc_detail'[Revenue]),
     FILTER(
        ALL('ccdb cc_detail'),
        'ccdb cc_detail'[WeekNum2] = MAX('ccdb cc_detail'[WeekNum2])-1 ))
```

## Project Insights
### Week over Week (WoW) Change
- Revenue increased by 28.8%
- Total Transaction Amount & Count increased by xx% & xx%
- Customer count increased by xx%

### Overview Year-to-Date (YTD)
- Overall revenue is 57M
- Total interest is 8M
- Total transaction amount is 46M
- Male customers are contributing more in revenue 31M, female 26M
- Blue & Silver credit cards are contributing to 93% of overall transactions
- TX, NY & CA are contributing to 68%
- Overall Activation rate is 57.5%
- Overall Delinquent rate is 6.06%

## Dashboard Images
![Credit Card Financial Dashboard- Transaction-1](https://github.com/user-attachments/assets/61de07f7-b76b-4406-9c3c-8aa772a82f44)
![Credit Card Financial Dashboard- Customer-1](https://github.com/user-attachments/assets/8e724bde-67d3-407d-ad53-b66bdb980e45)
