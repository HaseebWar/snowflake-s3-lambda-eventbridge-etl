# snowflake-s3-lambda-eventbridge-etl

A serverless ETL pipeline that fetches real-time currency exchange rates from the Open Exchange Rates API, stores raw data in S3, and processes it into Snowflake using AWS Lambda and EventBridge.

This project demonstrates a modern, cloud-native ETL pipeline using AWS services and Snowflake, suitable for data engineering, analytics, and automation use cases.

---

## Overview

This pipeline automates the end-to-end flow of currency exchange data:

1. Fetch data from the Open Exchange Rates API using AWS Lambda  
2. Store the raw JSON response in an Amazon S3 bucket  
3. Load and process the data in Snowflake using a stored procedure for further analysis  

---

## Architecture

![Architecture Diagram](https://github.com/username/snowflake-aws/raw/main/architecture-diagram.png)

- AWS Lambda: Executes the ETL logic on a schedule  
- Amazon S3: Stores raw exchange rate data as structured JSON files  
- AWS Secrets Manager: Secures Snowflake credentials  
- Snowflake: Stores and processes transformed exchange rate data  
- EventBridge: Triggers Lambda based on a cron schedule  

---

## Prerequisites

- AWS account with permissions for Lambda, S3, EventBridge, and Secrets Manager  
- Snowflake account with access to create databases and run SQL scripts  
- API key from Open Exchange Rates (https://openexchangerates.org/)  
- Python 3.9 (for local testing or deployment)  

---

## Setup Instructions

### AWS Setup

1. Create an IAM role with the following permissions:  
   - AmazonS3FullAccess  
   - AWSLambda_FullAccess  
   - SecretsManagerReadWrite  
   - AmazonEventBridgeFullAccess  

2. In AWS Secrets Manager, create a secret with ID `db/currency-exchange-rate`:  
   ```json
   {
     "fusion_snowflake": {
       "username": "your_username",
       "password": "your_password",
       "account_name": "your_snowflake_account"
     }
   }

### S3 Bucket Setup

Create an S3 bucket to store exchange rate files. Example:


---

### Snowflake Setup

Run the SQL script at `code/snowflake.sql` to:

- Create the database: `CURRENCY_DB`  
- Create the schema: `CURRENCY`  
- Create the tables: `EXCHANGE_RATES_RAW`, `EXCHANGE_RATES_STG`, `EXCHANGE_RATES`  
- Create the stored procedure: `SP_EXCHANGE_RATE_LOADING`  

---

### Lambda Setup

1. Create a new Lambda function using Python 3.9  

2. Upload the following files:
   - `code/lambda_function.py`  
   - `code/snowflake_provider.py`  

3. Set the following environment variables:

   | Variable            | Value                                             |
   |---------------------|---------------------------------------------------|
   | environment         | DEV                                               |
   | oer_app_id          | YOUR-APP-KEY                                      |
   | oer_base_currency   | USD                                               |
   | oer_base_url        | https://openexchangerates.org/api/latest.json     |
   | region_name         | us-east-1                                         |
   | s3_bucket_name      | s3-currency-exchange-rate-qh                      |
   | snowflake_db        | CURRENCY_DB                                       |
   | snowflake_role      | ACCOUNTADMIN                                      |
   | snowflake_wh        | COMPUTE_WH                                        |

4. Use EventBridge (CloudWatch Events) to schedule the Lambda run (e.g., hourly or daily)

## 🗂️ Project Structure

.
├── code/

│ ├── lambda_function.py 

│ ├── snowflake_provider.py 
│ └── snowflake.sql 

├── environment-variables.txt 

├── roles.txt 

├── secret-manager.txt 
└── convention.txt 

## 📈 Monitoring & Maintenance

- **AWS CloudWatch Logs**: Track Lambda executions, errors, and performance  
- **Snowflake History Tab**: Review executed queries, data loads, and stored procedure activity  
- **Data Freshness Check**: Query the `EXCHANGE_RATES` table regularly to verify new data is ingested
