# Advertising Spend and ROI Prediction

This demo was presented during the [Snowflake Summit Opening Keynote](https://events.snowflake.com/summit/agenda/session/849836). It is built using Snowpark For Python.

## Overview

In this workshop, we will train a Linear Regression model to predict future ROI (Return On Investment) of variable advertising spend budgets across multiple channels including search, video, social media, and email using Snowpark for Python and scikit-learn.

Workshop highlights:

* Set up your favorite IDE (e.g. Jupyter, VSCode) for Snowpark and ML
* Analyze data and perform data engineering tasks using Snowpark DataFrames
* Use open-source Python libraries from a curated Snowflake Anaconda channel with near-zero maintenance or overhead
* Deploy ML model training code on Snowflake using Python Stored Procedure
* Create and register Scalar and Vectorized Python User-Defined Functions (UDFs) for inference
* Create Snowflake Task to automate (re)training of the model

If all goes well, you should see the following app in your browser window.

https://user-images.githubusercontent.com/1723932/175127637-9149b9f3-e12a-4acd-a271-4650c47d8e34.mp4

## Prerequisites

  * As ACCOUNTADMIN role
    * Create a [Warehouse](https://docs.snowflake.com/en/sql-reference/sql/create-warehouse.html), a [Database](https://docs.snowflake.com/en/sql-reference/sql/create-database.html) and a [Schema](https://docs.snowflake.com/en/sql-reference/sql/create-schema.html)

## Setup

  ```sql
  USE ROLE ACCOUNTADMIN;
  ```

### **Step 1** -- Create Tables, Load Data and Setup Stages

* Create table CAMPAIGN_SPEND from data hosted on publicly accessible S3 bucket

  ```sql
  CREATE or REPLACE file format csvformat
    skip_header = 1
    type = 'CSV';

  CREATE or REPLACE stage campaign_data_stage
    file_format = csvformat
    url = 's3://sfquickstarts/Summit 2022 Keynote Demo/campaign_spend/';

  CREATE or REPLACE TABLE CAMPAIGN_SPEND (
    CAMPAIGN VARCHAR(60), 
    CHANNEL VARCHAR(60),
    DATE DATE,
    TOTAL_CLICKS NUMBER(38,0),
    TOTAL_COST NUMBER(38,0),
    ADS_SERVED NUMBER(38,0)
  );

  COPY into CAMPAIGN_SPEND
    from @campaign_data_stage;
  ```

* Create table MONTHLY_REVENUE from data hosted on publicly accessible S3 bucket

  ```sql
  CREATE or REPLACE stage monthly_revenue_data_stage
    file_format = csvformat
    url = 's3://sfquickstarts/Summit 2022 Keynote Demo/monthly_revenue/';

  CREATE or REPLACE TABLE MONTHLY_REVENUE (
    YEAR NUMBER(38,0),
    MONTH NUMBER(38,0),
    REVENUE FLOAT
  );

  COPY into MONTHLY_REVENUE
    from @monthly_revenue_data_stage;
  ```

* Create table BUDGET_ALLOCATIONS_AND_ROI that holds the last six months of budget allocations and ROI

  ```sql
  CREATE or REPLACE TABLE BUDGET_ALLOCATIONS_AND_ROI (
    MONTH varchar(30),
    SEARCHENGINE integer,
    SOCIALMEDIA integer,
    VIDEO integer,
    EMAIL integer,
    ROI float
  );

  INSERT INTO BUDGET_ALLOCATIONS_AND_ROI (MONTH, SEARCHENGINE, SOCIALMEDIA, VIDEO, EMAIL, ROI)
  VALUES
  ('January',35,50,35,85,8.22),
  ('February',75,50,35,85,13.90),
  ('March',15,50,35,15,7.34),
  ('April',25,80,40,90,13.23),
  ('May',95,95,10,95,6.246),
  ('June',35,50,35,85,8.22);
  ```

* Create stages required for Stored Procedures, UDFs, and saving model files

  ```sql
  CREATE OR REPLACE STAGE dash_sprocs;
  CREATE OR REPLACE STAGE dash_models;
  CREATE OR REPLACE STAGE dash_udfs;
  ```

## Notebook

### **Step 1** -- Clone or download repository

* `git clone https://github.com/Snowflake-Labs/snowpark-python-demos` OR `git clone git@github.com:Snowflake-Labs/snowpark-python-demos.git`

* `cd Advertising-Spend-ROI-Prediction`

### **Step 2** -- Create And Activate Conda Environment

* Note: You can download the miniconda installer from
https://conda.io/miniconda.html. OR, you may use any other Python environment with Python 3.8
  
* `conda create --name snowpark -c https://repo.anaconda.com/pkgs/snowflake python=3.8`

* `conda activate snowpark`

### **Step 3** -- Install Snowpark for Python, Streamlit and other libraries in Conda environment

* `conda install -c https://repo.anaconda.com/pkgs/snowflake snowflake-snowpark-python pandas notebook scikit-learn cachetools`

### **Step 4** -- Update [connection.json](connection.json) with your Snowflake account details and credentials. Use the same database, schema, and warehouse that is provided. 

* Note: For the **account** parameter, specify your [account identifier](https://docs.snowflake.com/en/user-guide/admin-account-identifier.html) and do not include the snowflakecomputing.com domain name. Snowflake automatically appends this when creating the connection.

### **Step 5** -- Train & deploy ML model

* In a terminal window, browse to the folder where you have this Notebook downloaded and run `jupyter notebook` at the command line
* Open and run through the [Jupyter notebook](Snowpark_For_Python.ipynb)
  * Note: Make sure the Jupyter notebook (Python) kernel is set to ***snowpark***

The notebook does the following...

* Performs Exploratory Data Analysis (EDA)
* Creates features for training a model and writes them to a Snowflake table
* Creates a Stored Proc for training a ML model and uploads the model to a Snowflake stage
* Calls the Stored Proc to train the model
* Creates Scalar and Vectorized User-Defined Functions (UDFs) that use the model for inference on new data points passed in as parameters
  * Note: The Scalar UDF is called from the below Streamlit app for real-time inference on new budget allocations based on user input
* Creates a Snowflake Task to automate (re)training of the model
