# Earthquake Azure Data Pipeline

... (Previous sections, including Architecture Overview, Data Modeling, Understanding the API, and Key Benefits) ...

## Architecture

**Visual Architecture Diagram**

![image](https://github.com/user-attachments/assets/5906b66a-e28a-4fc2-8788-c6185fc782b6)


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

... (Remaining sections) ...
