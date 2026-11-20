# Snowflake Data Engineering

* Snowflake let's you do ingestion, transformation, orchestration and observability.
* Snowflake let's you do batch and streaming ingestion.
* Snowflake helps with transformation of data with Dynamic tables, Snowpark DFs & more.
* Snowflake lets you do data orchestration with streams and tasks.
* Snowflake lets you monitor data pipelines with alerts, event tables and more

## Snowpipes
**Snowpipe** enables continuous, event-driven data loading — files are ingested automatically as they arrive in a stage.
- Snowpipe uses a `COPY INTO` statement defined inside a `CREATE PIPE` object.
- `AUTO_INGEST=TRUE` means Snowpipe listens for cloud event notifications (e.g., S3 SQS events) to trigger loading.
- A **storage integration** securely connects Snowflake to your cloud storage without embedding credentials in SQL.
- After creating a storage integration, run `DESCRIBE INTEGRATION` to get the IAM values needed for your cloud provider trust policy.
- Use `SHOW PIPES` and `DESCRIBE PIPE` to inspect pipe configuration.
- Pause pipes with `ALTER PIPE ... SET PIPE_EXECUTION_PAUSED = TRUE` before dropping them to avoid orphaned notifications.
