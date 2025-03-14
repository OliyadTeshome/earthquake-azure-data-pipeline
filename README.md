# Earthquake Azure Data Pipeline

This repository contains the code and documentation for an Azure Data Pipeline designed to process and analyze earthquake data from the USGS Earthquake API.

## Overview

This pipeline ingests raw earthquake data, transforms it into a usable format using a medallion architecture (Bronze, Silver, Gold), and stores it in Azure Data Lake Storage Gen2 (ADLS Gen2) for analysis. The pipeline leverages various Azure services to achieve this, including:

* **Azure Data Factory (ADF):** Orchestrates the entire pipeline, including data ingestion and workflow management.
* **Azure Data Lake Storage Gen2 (ADLS Gen2):** Stores raw and processed earthquake data at different stages.
* **Azure Databricks:** Performs complex data transformations and processing.
* **Azure Synapse Analytics:** Enables querying and aggregating data for reporting and analysis.
* **Power BI/Fabric/Tableau (Optional):** Used for data visualization and creating interactive dashboards.

## Architecture

**Architecture Overview**

This pipeline follows a modular architecture, integrating Azure's powerful data engineering tools to ensure scalability, reliability, and efficiency.

The architecture includes:

1.  **Data Ingestion:** Azure Data Factory orchestrates the daily ingestion of earthquake data from the USGS Earthquake API.
2.  **Data Processing:** Databricks processes raw data into structured formats (bronze, silver, gold tiers).
3.  **Data Storage:** Azure Data Lake Storage serves as the backbone for storing and managing data at different stages.
4.  **Data Analysis:** Synapse Analytics enables querying and aggregating data for reporting.
5.  **Optional Visualization:** Power BI, Fabric or Tableau can be used to create interactive dashboards for stakeholders.

**Visual Architecture Diagram**

![Earthquake Data Pipeline Architecture](your_image_filename.png "Earthquake Data Pipeline Architecture")

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

**Explanation of Tiers (Bronze, Silver, Gold)**

* **Bronze:** Raw data ingested directly from the USGS API, stored as is in Parquet format.
* **Silver:** Cleaned and standardized data, with basic transformations applied, removing duplicates and handling missing values.
* **Gold:** Aggregated and enriched data, optimized for reporting and analysis, tailored to specific business needs, such as adding country codes.

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

**Step 1: Create an Azure Account**

* Sign up for an Azure account if you do not already have one.

**Step 2: Create a Databricks Resource**

* Create a Databricks resource in Azure.
* Select the `Standard LTS (Long Term Support)` tier. Avoid using ML or other specialized tiers.

**Step 3: Set Up a Storage Account**

* Create a Storage Account and enable `hierarchical namespaces` in the advanced settings.
* Navigate to the Storage Account resource: Go to `Data Storage > Containers > + Containers`.
* Create three containers: `bronze`, `silver`, and `gold`.
* Configure access: Go to `IAM > Add role assignment > Storage Blob Data Contributor`.
* Click `Next > Managed Identity > Select Members`.
* Select `Access Connector for Azure Databricks` as the managed identity.
* Click `Review + Assign`.

**Step 4: Configure Databricks**

* Open the Databricks resource and click `Launch Workspace`.
* Start a compute instance (this may take a few minutes).
* Set up external data access: Go to `Catalog > External Data > Credentials > Create Credential`.
* For the `Access Connector ID`, use the Resource ID of the Access Connector: Search for `Access Connector`, copy the Resource ID, and paste it here.
* Use this section to grant permissions or delete credentials as needed.
* Define external locations: Navigate to `External Data > External Locations`.
* Assign a name, select the storage credential, and specify the URL (use the container name and storage account name for `bronze`, `silver`, and `gold`).
* For detailed steps, refer to this helpful video: [Pathfinder Analytics](link_to_video). (Replace `link_to_video` with the actual link).

**Step 5: Create and Execute Notebooks**

* In the Databricks workspace, create a notebook for each layer (bronze, silver, gold).
* Add the relevant code for `bronze` from GitHub.
* Execute the notebook and refresh the Storage Account containers to verify updates.
* Repeat the process for `silver` and `gold` notebooks, adding the corresponding code.

**Step 6: Install Required Libraries**

* Before running the `gold` notebook, install the `reverse_geocoder` library.
* Navigate to `Compute > Cluster > Libraries > + Install New Library`.
* Select `Source: PyPI` and enter `reverse_geocoder`.
* Wait a few minutes for the installation to complete.
* Use cluster-level libraries for consistency and shared environments across notebooks.

**Step 7: Optimize Gold Notebook Execution**

* During execution, you may encounter performance bottlenecks with the `reverse_geocoder` Python UDF due to its non-thread-safe nature in distributed setups.
* Replace the UDF with alternatives like:
    * Precomputed lookup tables.
    * Pandas UDFs for vectorized execution.
    * Batch processing geocoding outside Spark.

**Step 8: Set Up Azure Data Factory (ADF)**

* Create a new Azure Data Factory instance (in a new Resource Group if needed).
* Launch the ADF studio and create a pipeline: Drag the `Notebook` activity into the pipeline and configure it to run Databricks notebooks.
* Add a `Databricks Linked Service`: Use the `AutoResolveIntegrationRuntime`.
* Authenticate with an Access Token (recommended to store the token in a Key Vault for security).
* Pass parameters to the pipeline: For example, add parameters `start_date` and `end_date` with dynamic values using `@formatDateTime` expressions.
* Chain notebooks (bronze, silver, gold) to create a pipeline with success dependencies.
* Validate, publish, and run the pipeline.
* Schedule the pipeline to run at desired intervals (e.g., daily).

# Integrating Azure Synapse Analytics and Visualization Options

This document outlines the steps to integrate Azure Synapse Analytics with your data pipeline and provides guidance on visualization options.

## Step 9: Integrate Azure Synapse Analytics

1.  **Create a Synapse Workspace:**
    * Link it to the existing Storage Account where your data is stored.
2.  **Configure a File System and Permissions:**
    * Ensure Synapse has the necessary permissions to access your data in ADLS Gen2.
3.  **Query Data Using Serverless SQL:**
    * Use `OPENROWSET` to query Parquet files stored in your `bronze`, `silver`, and `gold` containers.
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
        * **Note:** Replace `<storage_account>` with the actual name of your storage account.
4.  **Create External Tables for Structured Access:**
    * Define external tables linked to the `gold` container for better organization and performance.
5.  **Optimize Performance:**
    * Use indexing, partitioning, and caching as required to improve query performance.

## Step 10: Visualization Options

* **Synapse SQL for Analytics:**
    * While Power BI can be used, Synapse SQL is a powerful alternative for analytics and queries, especially for Mac users.
* **Data Export:**
    * Export data from Synapse for further visualization or reporting if needed.

## Key Considerations

* **Linked Services:**
    * Ensure reusable and secure connections between Azure services.
* **Scalability:**
    * Utilize Synapse for querying large datasets efficiently.
* **Data Engineering Focus:**
    * Maintain an emphasis on structured pipelines and optimized workflows.

This guide provides a comprehensive approach to setting up a professional-grade Azure Databricks and Synapse workflow for data engineering.
