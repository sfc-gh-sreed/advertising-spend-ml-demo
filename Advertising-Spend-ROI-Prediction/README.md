# Advertising Spend and ROI Prediction

The source content for this demo was presented during the [Snowflake Summit Opening Keynote](https://events.snowflake.com/summit/agenda/session/849836). It is built using Snowpark For Python. It has been modified slightly for the purposes of this workshop.

## Overview

In this workshop, we will train a Linear Regression model to predict future ROI (Return On Investment) of variable advertising spend budgets across multiple channels including search, video, social media, and email using Snowpark for Python and scikit-learn.

Workshop highlights:

* Set up your favorite IDE (e.g. Jupyter, VSCode) for Snowpark and ML
* Analyze data and perform data engineering tasks using Snowpark DataFrames
* Use open-source Python libraries from a curated Snowflake Anaconda channel with near-zero maintenance or overhead
* Deploy ML model training code on Snowflake using Python Stored Procedure
* Create and register Scalar and Vectorized Python User-Defined Functions (UDFs) for inference
* Create Snowflake Task to automate (re)training of the model


## Setup Instructions

### **Step 1** -- Clone or download repository

* `git clone https://github.com/sfc-gh-sreed/snowpark-ml-demo` OR `git clone git@github.com:sfc-gh-sreed/snowpark-ml-demo.git` 

* `cd Advertising-Spend-ROI-Prediction`

### **Step 2** -- Create And Activate Conda Environment

* Note: You can download the miniconda installer from
https://conda.io/miniconda.html. OR, you may use any other Python environment with Python 3.8
  
* `conda create --name snowpark -c https://repo.anaconda.com/pkgs/snowflake python=3.8`

* `conda activate snowpark`

### **Step 3** -- Install Snowpark for Python and other libraries in Conda environment

* `conda install -c https://repo.anaconda.com/pkgs/snowflake snowflake-snowpark-python pandas notebook scikit-learn cachetools`

### **Step 4** -- Update [connection.json](connection.json) with your Snowflake account details and credentials. Use the same database, schema, and warehouse that is provided. 

* Note: For the **account** parameter, specify your [account identifier](https://docs.snowflake.com/en/user-guide/admin-account-identifier.html) and do not include the snowflakecomputing.com domain name. Snowflake automatically appends this when creating the connection.

### **Step 5** -- Train & deploy ML model

* In a terminal window, browse to the folder where you have this Notebook downloaded and run `jupyter notebook` at the command line
* Open and run through the [Snowpark_For_Python.ipynb](Snowpark_For_Python.ipynb) notebook
* As you go through the exercises, refer to the [Snowpark_For_Python_Solution.ipynb](Snowpark_For_Python_Solution.ipynb) notebook for the answers
  * Note: Make sure the Jupyter notebook (Python) kernel is set to ***snowpark***
