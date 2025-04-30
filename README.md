# Sentimental-Analysis

The project outlines the process of downloading and preparing a large dataset of Yelp reviews and business information, ingesting the data into Snowflake, and converting JSON data into a tabular format. It then demonstrates creating a Python User-Defined Function (UDF) within Snowflake to perform sentiment analysis on reviews and finally showcases how to analyze the structured data using SQL queries to answer various business-related questions. The presenter emphasizes techniques for handling large files and using Snowflake's features for efficient data processing and analysis.

The project involves performing data analysis and sentimental analysis on Yelp reviews data using various technologies. The main dataset is a large JSON file (~5GB) containing review and business information.
Here are the key steps and technologies used:

Step 1: Data Download

Download the Yelp open dataset, which is available as a JSON file. This file contains several sub-files, but the main ones used are reviews.json and businesses.json. The reviews.json file is significantly larger (~5GB with 7 million reviews) than the businesses.json file (~100MB).
•
Step 2: Splitting the Large Data File
◦
The large reviews.json file needs to be split into multiple smaller files (e.g., 20-25 files of around 200-500MB each) using a Python program. This is crucial because ingesting a single large file takes significantly longer in Snowflake as it prevents parallel processing. The smaller businesses.json file does not require splitting.

Step 3: Uploading Data to Cloud Storage
◦
The split JSON files (and the unsplit businesses file) are uploaded to an AWS S3 bucket. This step involves setting up an AWS account, creating an S3 bucket, and uploading the files.

To allow Snowflake to read from S3, an AWS IAM user is created with S3 read-only access, and the access key and secret access key are obtained.

Alternatively, files can be loaded directly into Snowflake from local storage, but using S3 is generally faster for larger datasets and allows for parallel loading.

◦
Create a new database in Snowflake.
◦
Open a SQL worksheet in Snowflake to execute commands.
•
Step 5: Creating Staging Tables in Snowflake
◦
Create initial staging tables in Snowflake (yelp_reviews, yelp_businesses) to load the raw JSON data. These tables are created with a single column using the VARIANT data type, which is suitable for storing semi-structured data like JSON.
•
Step 6: Loading Data into Snowflake
◦
Use the Snowflake COPY INTO command to ingest the data from the AWS S3 bucket into the staging tables created in Step 5. The COPY INTO command specifies the S3 path, file format (JSON), and uses the AWS IAM credentials (access key and secret access key) to access the data. The splitting done in Step 2 allows Snowflake to load these files in parallel, significantly speeding up the ingestion process.
•
Step 7: Creating a User Defined Function for Sentiment Analysis
◦
Create a Python User Defined Function (UDF) directly within Snowflake using the CREATE FUNCTION statement.
◦
This Python UDF utilizes the textblob Python library to perform sentiment analysis on review text. The function takes review text as input and returns a sentiment label (positive, neutral, or negative).

Step 8: Converting JSON to Tabular Format and Creating Final Tables

Create final, structured tables in Snowflake (TBL_Yelp_Reviews, TBL_Yelp_Businesses) by extracting specific fields from the raw JSON data stored in the VARIANT column of the staging tables.

This is done using SQL, referencing JSON elements with the colon notation (column_name:element_name) and casting them to appropriate data types (e.g., STRING, DATE, NUMBER).

The Python UDF for sentiment analysis is applied to the review text column during the creation of the TBL_Yelp_Reviews table to add a sentiment column.

To speed up the table creation process, especially when applying the UDF to millions of rows, the compute power of the Snowflake Warehouse can be increased (e.g., to Extra Large).

Step 9: Performing Data Analysis with SQL

◦
Specific Snowflake SQL functions and features are used, such as SPLIT_TO_TABLE with LATERAL and TRIM for splitting comma-separated categories into multiple rows, window functions like ROW_NUMBER for ranking, and the QUALIFY clause to filter results based on window function outputs. Case statements are also used, for example, to count specific types of reviews
