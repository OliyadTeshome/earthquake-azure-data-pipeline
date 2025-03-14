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

![image](https://github.com/user-attachments/assets/fb8f6d50-fe8b-40e6-bb55-59c6c6b6e011)


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

## Getting Started

1.  **Clone the repository:**

    ```bash
    git clone [repository URL]
    cd earthquake-azure-data-pipeline
    ```

2.  **Deploy Azure Resources:**
    * Use the Azure portal, Azure CLI, or Azure PowerShell to create the required Azure resources (ADF, ADLS Gen2, Databricks, Synapse).
    * If using infrastructure as code (IaC) such as Terraform or ARM templates, deploy those from this repository.

3.  **Configure Azure Data Factory:**
    * Create linked services in ADF to connect to the USGS API, ADLS Gen2, Databricks, and Synapse.
    * Import the ADF pipelines and datasets from the repository.
    * Configure the triggers for daily ingestion.

4.  **Configure Databricks:**
    * Import the Databricks notebooks into your workspace.
    * Configure the notebook parameters and dependencies for Bronze to Silver and Silver to Gold transformations.

5.  **Configure Synapse Analytics:**
    * Create external tables in Synapse to access the Gold layer data in ADLS Gen2.
    * Develop SQL queries and views for reporting and analysis.

6.  **Configure Visualization (Optional):**
    * Connect Power BI/Fabric/Tableau to Synapse Analytics to create dashboards and reports.

7.  **Run the Pipeline:**
    * Manually trigger the ADF pipeline or wait for the scheduled trigger to execute.
    * Monitor the pipeline execution in Azure Monitor and ADF monitoring.

## Configuration

* **Connection Strings:** Store connection strings and API keys securely using Azure Key Vault and reference them in ADF linked services.
* **Parameters:** Use ADF pipeline parameters and Databricks notebook parameters for configurable settings.
* **File Paths:** Configure file paths in datasets and activities to match your storage account structure.

## Contributing

Contributions are welcome! Please follow these steps:

1.  Fork the repository.
2.  Create a new branch for your feature or bug fix.
3.  Commit your changes.
4.  Push your branch to your fork.
5.  Submit a pull request.

## Future Enhancements

* Implement real-time data ingestion using Azure Event Hubs.
* Add more advanced geospatial analysis in Databricks.
* Integrate machine learning models for earthquake prediction.
* Add more robust error handling and alerting.
* Add automated testing.

**Important:** Remember to replace `[repository URL]`, `your_image_filename.png`, and `[Your License]` with your actual information.
