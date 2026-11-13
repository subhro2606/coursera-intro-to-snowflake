[Github repo](https://github.com/Snowflake-Labs/sf-samples/tree/main/samples/intro-to-snowflake-for-devs-data-scientists-data-engineers)

## Stages and Basic Ingestion

Stage is an intermediate data object that serves as a bridge between datasource and table.

<<<<<<< local
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
* We can trigger manual refresh of dynamic tables with `ALTER DYNAMIC TABLE <table_name> REFRESH

## Views

=======
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

>>>>>>> remote