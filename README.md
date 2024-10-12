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
   COPY cust_detail(customer_id, customer_name, customer_age, income)
   FROM '/path/to/customer.csv' DELIMITER ',' CSV HEADER;

   COPY cc_detail(transaction_id, customer_id, annual_fees, total_trans_amt, interest_earned, week_start_date)
   FROM '/path/to/credit_card.csv' DELIMITER ',' CSV HEADER;
   ```

## DAX Queries

### Create AgeGroup Column
```dax
AgeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[customer_age] < 30, "20-30",
    'public cust_detail'[customer_age] >= 30 && 'public cust_detail'[customer_age] < 40, "30-40",
    'public cust_detail'[customer_age] >= 40 && 'public cust_detail'[customer_age] < 50, "40-50",
    'public cust_detail'[customer_age] >= 50 && 'public cust_detail'[customer_age] < 60, "50-60",
    'public cust_detail'[customer_age] >= 60, "60+",
    "unknown"
)
```

### Create IncomeGroup Column
```dax
IncomeGroup = SWITCH(
    TRUE(), 
    'public cust_detail'[income] < 35000, "Low",
    'public cust_detail'[income] >= 35000 && 'public cust_detail'[income] < 70000, "Med",
    'public cust_detail'[income] >= 70000, "High",
    "unknown"
)
```

### Calculate Revenue
```dax
Revenue = 'public cc_detail'[annual_fees] + 'public cc_detail'[total_trans_amt] + 'public cc_detail'[interest_earned]
```

### Calculate Week Number
```dax
week_num2 = WEEKNUM('public cc_detail'[week_start_date])
```

### Current Week Revenue
```dax
Current_week_Revenue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])
    )
)
```

### Previous Week Revenue
```dax
Previous_week_Revenue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])-1
    )
)
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
