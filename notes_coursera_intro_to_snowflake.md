[Github repo](https://github.com/Snowflake-Labs/sf-samples/tree/main/samples/intro-to-snowflake-for-devs-data-scientists-data-engineers)


## Virtual Resources
A cluster of compute resources.

We can scale warehouse:
* Vertically: Compute doubles each time you go up a size. Size: XS, S, M, L, XL, 2XL, 3XL upto 6XL -> relative compute power 1, 2, 4, 8, 16, 32, 64 upto 512 
* Horizontally

### GPU-optimized warehouses

Snowflake now offers GPU-optimized warehouses in addition to Standard and Snowpark-optimized options. These are designed for machine learning and AI workloads, including model training, inference, and containerized workloads running through Snowpark Container Services. For SQL queries and standard data transformations, GPU warehouses are not required. Creating one is identical to creating any other warehouse and the GPU type is available in the Type dropdown in Snowsight.

### Snowpark-optimized warehouses

The video describes Snowpark-optimized warehouses as having extra memory. More precisely, they provide significantly more memory per node than standard warehouses of equivalent size. This matters for memory-intensive Python workloads run through Snowpark, such as loading large datasets into a DataFrame or training machine learning models. If a Snowpark job encounters memory limitations on a standard warehouse, switching to a Snowpark-optimized warehouse of the same size is typically the appropriate first step.

### Serverless compute

Not all Snowflake features consume credits through a user-managed virtual warehouse. A growing set of features, including Snowpipe, certain machine learning functions, serverless tasks, and Cortex Search services, run on Snowflake-managed serverless compute. You will encounter this as you progress through the course. The practical implication is that credit consumption can occur without an active virtual warehouse, which is expected behavior for these feature types.

### Query Acceleration Service

The Query Acceleration Service (QAS) is a warehouse-level setting that offloads eligible portions of a query to serverless compute. It is designed for workloads with variable query complexity, where most queries are straightforward but occasional large queries against high-cardinality data cause performance degradation. Snowflake automatically identifies queries that would benefit from acceleration and routes the eligible portions accordingly. QAS is configured when creating or editing a warehouse in Snowsight.


## Stages and Basic Ingestion

Stage is an intermediate data object that serves as a bridge between datasource and table.

* User Stage: Can be used by that specific user. Can't be dropped.
* Table Stage: Can be associated with the associated table. Cant be dropped
* Named Stage: Can be used by multiple users and be associated with multiple tables.
    *  External Stage: has reference to a cloud storage bucket
    *  Internal Stage
  

## Storage integrations as the recommended authentication pattern

The video creates an external stage using inline credentials. While functional, this is not recommended for production environments. Inline credentials are more difficult to rotate and audit. The preferred approach is to use a storage integration object, which stores the authentication details separately from the stage definition. When creating a stage, the storage integration is referenced by name rather than including credentials directly. This simplifies credential rotation and improves auditability.

### Directory tables

When creating a stage, a directory table can be enabled on it. A directory table automatically tracks metadata for the files in the stage, including file names, sizes, and last-modified timestamps. Once enabled, it can be queried directly using a statement like this:

SELECT * FROM DIRECTORY(@my_stage);

### Directory tables are useful for building ingestion pipelines that need to identify which files are present before determining what to load, and for patterns where only new or unprocessed files should be ingested.

### Snowpipe Streaming

The video covers batch ingestion using COPY INTO. For use cases requiring continuous, low-latency ingestion, Snowpipe Streaming provides a distinct capability covered in more depth in Module 3. Rather than ingesting files, it ingests individual rows through a Java SDK with latency measured in seconds. Learners building pipelines where a file-based ingestion pattern cannot meet latency requirements should refer to that section.

## Databases and schemas

* Database: parent folder. In snowflake we have CREATE, DROP, UNDROP, SHOW, USE database commands. collection of one or more schemas.
* schemas: subfolder. By default when we create a database we have the PUBLIC and INFORMATION_SCHEMA schemas. We can use the `INFORMATION_SCHEMA` to query specific metadata about tables and views. DESCRIBE <data_base> gives us the list of schemas in a database. we have CREATE, DROP, UNDROP, SHOW, USE schemas commands. collections of tables and views
* table & views: data objects

### Database Explorer
Allows for inspection of our data. Shows the hierarchy of the data. Allows for inspection and preview of the data. Shows teh data lineage.

## Tables
In snowflake the data within a table is broken into micro partitions.

6 categories of snowflake data types 
* Numeric
* String and Binary
* Logical
* Date and time
* Semi-structured
* Geospacial

We can `CREATE`, `DROP`, `UNDROP`, `SHOW` tables and `INSERT` data into table


### Dynamic Tables
Dynamic tables stores results of a query and refresh automatically on a schedule you define.
* The `TARGET_LAG` parameter controls how current the data stays
* Unlike materialized views Dynamic tables supports joins and complex transformations
* Use `SHOW DYNAMIC TABLES` to view details about dynamic tables
* We can trigger manual refresh of dynamic tables with `ALTER DYNAMIC TABLE <table_name> REFRESH`

## Views

* Standard Views: saves the query, runs every time we query it, can have join
* Materialized Views: saves the result of running a query and automaticlly updates the view whenever the underlying tables updates. stores precomputed results just like dynamic tables but these doesn't support joins. Snowflake recommend Dynamic Tables instead of materialized views and dynamic tables are the new approach.
* Secure View: Query defination is hidden from everyone other than owner. Allows sharing data without revealing the view defination. `SECURE` keyword is used. Bypass the query optimizer.

We can CREATE, DROP, ALTER, DESCRIBE, SHOW views


## Semistructured Data
Snowflake supports the following semistructured json format:
* JSON
* Avro
* ORC
* Parquet
* XML

Native Snowflake Data Types
* VARIANT: Variant is a flexible datatype in snowflake that can hold any data type, and snowflake keeps track of the datatype and allows corresponding operation
* OBJECT: It's like a dictionary in python, key value pair, value is of type variant. Can be accessed using obj["key"] notation or object:key
* ARRAY: List of ordered entries. Each element of the ARRAY is of the VARIANT type. Can be accessed like arr[0]

## Snowflake architeture
* Optimized storage:
  * Access to data all at one place.
  * Easily manage data at scale. 
  * Flexibility and Interoperability
* Elastic Multicluster compute
  * Separates storage from compute. 
  * We can have separate storage clusters operating on same or differnt datasets ensuring concurrency and performance
  * We can work with sql, python or java and preffered libraries in snowpark.
  * bring developement to data
* Cloud Services
    * This layer manages files and file metadata, enabling ACID transactions, query result caching, time travel, zero copy cloning and high concurrency
* Snowgrid: Cross cloud and global
    * Allows businesses connect across regions and cloud
    * Cross cloud collaboration with no etl
    * Cross cloud communication to minimize risk
    * cross cloud business continuity to eliminate disruptions.

### AI Data Cloud positioning

Snowflake now refers to the platform as the AI Data Cloud. This reflects the integration of AI capabilities directly into the platform rather than treating them as a separate product. Snowflake Cortex, covered more in Module 3, is built into the platform alongside storage and compute. The same governance policies and access controls that apply to data objects apply to AI workloads, meaning there is no separate AI infrastructure to manage or secure independently.

### Snowpark Container Services in the compute layer

The compute layer described in the video centers on virtual warehouses. Snowpark Container Services (SPCS) extends the compute layer to support containerized workloads running within Snowflake's infrastructure. This includes code written in languages or frameworks Snowflake does not natively support, as well as GPU-accelerated workloads for machine learning. SPCS is covered in Module 3. Its architectural significance is that it broadens what "compute" means within the platform beyond SQL-based query execution.

### Hybrid Tables

Standard Snowflake tables are optimized for analytical workloads. Snowflake has introduced Hybrid Tables to support transactional workloads within the same platform. Hybrid Tables support primary key enforcement, unique constraints, indexes for fast row-level lookups, and row-level locking. This allows applications requiring both transactional and analytical capabilities to use a single platform rather than maintaining separate systems for each workload type.