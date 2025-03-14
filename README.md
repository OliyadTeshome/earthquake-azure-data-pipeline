# Earthquake Azure Data Pipeline

This repository contains the code and documentation for an Azure Data Pipeline designed to process and analyze earthquake data from the USGS Earthquake API.

## Overview

This pipeline ingests raw earthquake data, transforms it into a usable format using a medallion architecture (Bronze, Silver, Gold), and stores it in Azure Data Lake Storage Gen2 (ADLS Gen2) for analysis. The pipeline leverages various Azure services to achieve this, including:

* **Azure Data Factory (ADF):** Orchestrates the entire pipeline, including data ingestion and workflow management.
* **Azure Data Lake Storage Gen2 (ADLS Gen2):** Stores raw and processed earthquake data at different stages.
* **Azure Databricks:** Performs complex data transformations and processing.
* **Azure Synapse Analytics:** Enables querying and aggregating data for reporting and analysis.
* **Power BI:** Used for data visualization and creating interactive dashboards.

## Architecture

![image](https://github.com/user-attachments/assets/243f8d0a-942d-4a3d-948f-baca656b3602)


**Explanation of Components**

* **Source (API Endpoint):** The USGS Earthquake API providing the raw earthquake data.
* **Azure Data Factory:** Orchestrates the ingestion of data from the API to ADLS Gen2.
* **ADLS Gen2:** Azure Data Lake Storage Gen2, used for storing data at different stages:
    * **Bronze (3):** Raw data directly from the API.
    * **Silver (2):** Cleaned and transformed data.
    * **Gold (1):** Aggregated and enriched data.
* **Azure Databricks:** Performs data transformation and processing, moving data from Bronze to Silver to Gold layers.
* **Azure Synapse Analytics:** Used for querying and analyzing the Gold layer data for reporting.
* **Visualization (Fabric, Power BI, Tableau):** Tools used for creating interactive dashboards and reports.

## Data Modeling

We implement a medallion architecture to structure and organize data effectively:

1.  **Bronze Layer:** Raw data ingested directly from the API, stored in Parquet format for future reprocessing if needed.
2.  **Silver Layer:** Cleaned and normalized data, removing duplicates and handling missing values, ensuring it's ready for analytics.
3.  **Gold Layer:** Aggregated and enriched data tailored to specific business needs, such as adding in country codes.

## Understanding the API

* The earthquake API provides detailed seismic event data for a specified start and end date.
* **Start Date:** Defines the range of data. This is dynamically set via Azure Data Factory for daily ingestion.
* **API URL:** `https://earthquake.usgs.gov/fdsnws/event/1/`

## Key Benefits

* **Automation:** Eliminates manual data fetching and processing, reducing operational overhead.
* **Scalability:** Handles large volumes of data seamlessly using Azure services.
* **Actionable Insights:** Provides stakeholders with ready-to-use data for informed decision-making.

## Step-by-Step Setup Guide

This guide provides a comprehensive approach to setting up a professional-grade Azure Databricks and Synapse workflow for data engineering.

**Step 1: Create a Resource Group**

**Step 2: Set Up a Storage Account**

**Step 3: Create a Databricks Resource**

**Step 4: Configure Databricks**

**Step 5: Create and Execute Notebooks**

* Bronze Notebook
* Silver Notebook
* Gold Notebook

**Step 7: Set Up Azure Data Factory (ADF)**

**Step 9: Integrate Azure Synapse Analytics**

1.  Create a Synapse Workspace
3.  Configure a File System and Permissions
4.  Query Data Using Serverless SQL
    * **Example Query:**
        ```sql
        SELECT
            country_code,
            COUNT(CASE WHEN LOWER(sig_class) = 'low' THEN 1 END) AS low_count,
            COUNT(CASE WHEN LOWER(sig_class) IN ('medium', 'moderate') THEN 1 END) AS medium_count,
            COUNT(CASE WHEN LOWER(sig_class) = 'high' THEN 1 END) AS high_count
        FROM
            OPENROWSET(
                BULK 'https://<storage_account>.dfs.core.windows.net/gold/earthquake_events_gold/**',
                FORMAT = 'PARQUET'
            ) AS [result]
        GROUP BY
            country_code;
        ```
5.  Create External Tables for Structured Access
6.  Optimize Performance
   
**Step 10: Visualization Options**

* Synapse SQL for Analytics
   
* Data Export

## Key Considerations

* **Linked Services:**
    * Ensure reusable and secure connections between Azure services.
* **Scalability:**
    * Utilize Synapse for querying large datasets efficiently.
* **Data Engineering Focus:**
    * Maintain an emphasis on structured pipelines and optimized workflows.
