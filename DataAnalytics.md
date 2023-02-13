# Data Analytics in Azure

*************************

### Large-scale Data Warehouse in Azure

Large-scale data warehousing solutions combine conventional data warehousing used to support business intelligence (BI) with techniques used for so-called big data analytics. A conventional data warehousing solution typically involves copying data from transactional data stores into a relational database with a schema that's optimized for querying and building multidimensional models. Big Data processing solutions are used with large volumes of data in multiple formats, with is batch loaded or captured in real-time streams and stored in a data lake from which distributed processing engines are used to process it.

Large-scale data warehousing architecture can vary, as can the specific technologies used to implement it, but in general, the following elements are included:

1. Data ingestion and processing - data from transactional data stores, files, real-time streams, or other sources is loaded into a data lake or a relational data warehouse. The load operation usually involves an extract, transform, and load (ETL) or extract, load, and transform (ELT) process in which the data is cleaned, filtered and restructured for analysis. In ETL, the data ins transformed before being loaded into an analytical store, while in an ELT the data is copied to the store and then transformed. The resulting data structure is optimized for analytical queries. Data processing is often performed by distributed systems that can process high volumes of data in parallel using multi-node clusters. Data ingestion includes both batch processing of static data and real-time processing of streaming data.
2. Analytical data store - include relational data warehouses, file-system based data lakes, and hybrid architectures that comibne features of data warehouses and data lakes (sometimes called data lakehouses or lake databases).
3. Analytical data model - while data analysts and data scientists can work with the data directly in the analytical data store, it's common to create one or more data models that pre-aggregate the data to make it easier to produce reports, dashboards, and interactive visualizations. These data models are described as cubes, in which numeric data values are aggregated across one or more dimensions.
4. Data visualization - data analysts consume data from analytical models, and directly from analytical stores to create reports, dashboards, and other visualizations. Additionally, users in an organization who may not be technology professionals might perform self-service data analysis and reporting. The visualizations from the data show trends, comparisons, and key performance indicators (KPIs) for a business or other other organizations, and can take the form on printed reports, graphs and charts.

![large-scale datawarehousing](img/large-scale-dw.jpg)

#### Data Ingestion Pipelines in Azure

Large-scale data ingestion is best implemented by creating pipelines that orchestrate ETL precesses. You can creande and run pipelines using Azure Data Factory or Azure Synapse Analytics if you want to manage all of the components of your data warehousing in a unified workspace.
Pipelines consist of one or more activities that operate on data. An input dataset provides the data source, and activities can be defined as a data flow that incrementally manipulates the data until an output dataset is produced. Pipelines use linked services to load and process data.

![data ingestion pipeline](img/data-ingestion-pipeline.jpg)

#### Analytical Data Stores

There are two common types of analytical data store.

##### Data warehouses

A relational database in which the data is stored in a schema that is optimized for data anlytics rather than transactional workloads. The data from a transactional store is transformed into a schema in which numeric values are stored in central fact tables, which are related to one or more dimension tables that represent entities by which the data can be aggregated. A fact table might contain sales order data, which can be aggregated by customer, product, store, and time dimensions. This kind of fact and dimension table schema is called a star schema; it's often extended into a snowflake schema by adding additional tables related to the dimension tables to represent dimensional hierarchies (product might be related to product categories). A data warehouse is a great choice when you have transactional data that can be organized into a structured schema of tables, and you want to use SQL to query them.

##### Data Lakes

A file store, usually on a distributed file system for high performance data access. Technologies like Spark and Hadoop are used to process queries on the stored files and return data for reporting and analytics. These systems apply a schema-on-read approach to define tabular schemas on semi-structured data files at the point where the data is read for analysis, without applying constraints when it's stored. Data lakes are great for supporting a mix of structured, semi-structured and unstructured data that you want to analyze without the need for schema enforcement when the data is written to the store.

##### Hybrid approaches

Combines features of data lakes and data warehouses in a lake database or data lakehouse. The raw data is stored as files in a data lake, and a relational storage layer abstracts the underlying files and expose them as tables, which can be queried using SQL. SQL pools in Azure Synapse Analytics include PolyBase, which enables you to define external tables based on files in a datalake (and other sources) and query them using SQL. Synapse Analytics also supports a Lake Database approach in which you can use database templates to define the relational schema of your data warehouse, while storing the underlying data in data lake storage, separating the storage and compute for your data warehousing. Data lakehouses are relatively new in Spark-based systems, and are enabled through technologies like Delta Lake, which adds relational storage capabilities to Spark, so you can define tables that enforce schemas and transactional consistency, support batch-loaded and streaming data sources, and provide a SQL API for querying.

#### Azure Services for Analytical Stores

There are three main services to implement large-scale analytical store

- Azure Synapse Analytics - unified, end-to-end solution, brings together multiple technologies and capabilities, enabling to combine the data integrity and reliability of a scalable, high-performance SQL Server based relational data warehouse with the flexibility of a data lake and open-source Apache Spark. Includes native support for log and telemety analytics with Azure Synapse Data Explorer pools, as well as built in data pipelines for data ingestion and transformation. All services can ben maganed through a single interface called Azure Synapse Studio, which includes the ability to create interactive notebooks in which Spark code and markdown content can be combined. Great choice when you want to create a single unified analytics solution on Azure.
- Azure Databricks - Azure implementation of the popular Databricks platform. Is a data analytics solution built on Spark and offers native SQL capabilities as well as workload-optimized Spark clusters for data analytics and data science. Provides an interactive user interface through which the system can ben managed and data can be explored in interactive notebooks. Might consider using Azure Databricks as your analytical store if you want to use existing expertise with the platform, or if you need to operate in a multi-cloud envirorment or support a cloud-portable solution.
- Azure HDInsight - service that supports multiple open-source data analytics cluster types. Is not as user-friendly as Azure Synapse Analytics and Azure Databricks. Can be a suitable option if your analytics solution relies on multiple open-source frameworks or if you need to migrate an existing on-premises Hadoop-based solution to the cloud.
