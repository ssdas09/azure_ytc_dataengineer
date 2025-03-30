# Building an End-to-End Data Pipeline for NYC Taxi Trip Analysis

## Introduction

"Data is the new oil," they say, and in the bustling streets of New York City, a wealth of data lies within the taxi trips. I embarked on a project to extract, transform, and load (ETL) this data, creating an end-to-end data pipeline using Azure Data Factory, Azure Databricks, and Power BI. This project aimed to analyze the 2024 green taxi trip data, providing valuable insights into travel patterns and trends.

## Phase 1: Data Ingestion with Azure Data Factory (ADF)

* **1.1 Setting up Azure Data Lake Gen2 (ADLS Gen2):**
    * Created an Azure Storage Account configured for ADLS Gen2 with hierarchical namespace enabled.
    * Created three containers: `bronze`, `silver`, and `gold`.
    * Created folders within `bronze`: `trips2k24data`, `trip_zone`, and `trip_type`.
* **1.2 Creating a Parameterized Pipeline in ADF:**
    * Created a new pipeline in Azure Data Factory.
    * Used a parameter `monthNumber` to iterate through months (01 to 12).
    * Used a "ForEach" activity to loop through each month.
    * Added a "Copy Data" activity inside the loop.
* **1.3 Configuring the Source Dataset:**
    * Configured an HTTP dataset pointing to `https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2024-XX.parquet`.
    * Used the `monthNumber` parameter to dynamically replace "XX".
* **1.4 Configuring the Sink Dataset:**
    * Configured a Parquet dataset pointing to `bronze/trips2k24data` in ADLS Gen2.
    * Dynamically generated filenames using the month number.
* **1.5 Uploading Lookup Data:**
    * Uploaded `trip_zone.csv` to `bronze/trip_zone` and `trip_type.csv` to `bronze/trip_type` using Azure Storage Explorer.
* **1.6 Pipeline Execution and Monitoring:**
    * Triggered and monitored the pipeline run in ADF.
    * Verified successful data copy to `bronze/trips2k24data`.

## Phase 2: Data Transformation with Azure Databricks (Silver Layer)

* **2.1 Setting up Azure Databricks:**
    * Created an Azure Databricks workspace and launched a Databricks cluster.
* **2.2 Service Principal Authentication:**
    * Created an Azure Active Directory service principal.
    * Granted Storage Blob Data Contributor permissions to the service principal on ADLS Gen2.
    * Stored service principal credentials as secrets in Databricks secrets scope.
    * Authenticated Databricks to ADLS Gen2 using the service principle.
* **2.3 Creating the "silver_layer.ipynb" Notebook:**
    * Created a new notebook "silver_layer.ipynb" in the "nyc taxi" folder.
* **2.4 Reading Raw Data:**
    * Read Parquet files from `bronze/trips2k24data` and CSV files from `bronze/trip_zone` and `bronze/trip_type` into Spark DataFrames.
* **2.5 Data Transformation:**
    * Performed data cleaning, transformation, and enrichment:
        * Data type conversions.
        * Filtering invalid data.
        * Joining lookup data.
        * Creating derived columns.
        * Data quality checks.
* **2.6 Writing Transformed Data:**
    * Wrote the transformed DataFrames to `silver` container in Parquet format.
* **2.7 Notebook execution:**
    * Ran the notebook and verified the output.

## Phase 3: Data Warehousing with Azure Databricks (Gold Layer)

* **3.1 Creating the "gold_layer.ipynb" Notebook:**
    * Created a new notebook "gold_layer.ipynb" in the "nyc taxi" folder.
* **3.2 Creating Delta Tables:**
    * Read transformed data from the `silver` container using Spark and Delta Lake.
    * Created Delta tables from the DataFrames.
* **3.3 Storing Delta Tables as External Tables:**
    * Stored Delta tables as external tables in the `gold` container.
* **3.4 Notebook execution:**
    * Ran the notebook and verified the output.

## Phase 4: Data Visualization with Power BI

* **4.1 Connecting Power BI to Databricks:**
    * Used the Databricks connector in Power BI Desktop.
* **4.2 Service Principal Authentication in Power BI:**
    * Authenticated with Databricks using the service principal.
* **4.3 Importing Delta Tables:**
    * Imported Delta tables from the `gold` container into Power BI.
* **4.4 Creating Dashboards and Reports:**
    * Created interactive dashboards and reports:
        * Trip frequency by location.
        * Average trip duration.
        * Fare distribution.
        * Trip type analysis.
        * Geographic mapping.
* **4.5 Publishing and Sharing:**
    * Published and shared the Power BI report.

## Conclusion

This project demonstrated the power of Azure's data services in building a comprehensive data pipeline. By meticulously executing each step, from data ingestion to visualization, I was able to transform raw taxi trip data into valuable insights. This end-to-end approach highlights the importance of data engineering in unlocking the potential of data for informed decision-making.
