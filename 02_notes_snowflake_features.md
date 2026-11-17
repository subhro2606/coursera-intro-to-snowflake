# Snowflake Feature Overview

## Collaborative Developement

### Git Integration 
Snowflake integrates with Git. It is a synchronized clone of your remote Git repository that allows Snowflake to reference files directly from your codebase.
* Works with GitHub, GitLab, and Bitbucket
* Notebooks support both reading from and writing back to a connected repository. Read-only access describes how other Snowflake objects like SQL files reference the repository stage, not how Notebooks interact with it.

### Data clean room
Data clean room allow for data-safe cross organizational colaborations
* The provider defines the approved query templates that determine what analyses can be performed and what results are returned.
* Consumer can query the aggregated results that meet minimum threshold requirements, with no access to the provider's individual records


## Time travel and Table Types

### Time Travel
Snowflake time travel allows us to go back in time and get back data at a time in the recent past.
 * This is controlled by `RETENTION_TIME`. Default `RETENTION_TIME` is 1, meaning one day. For enterprise edition of snow flake we can change this using `ALTER table` command.
 * Say we set `RETENTION_TIME` to 90, that means we can query the table at any point upto 90 days.
 * UNDROP table, UNDROP schema etc also works beecause of retention period.

We can query a table with Time Travel:
* we can query a table as of a timestamp
* we can query a table as of certain number of seconds in the past
* we can also query a table before a certain past query was run.

Time travel applies to Tables and Views as well.

**Tips:**
- **Time Travel** lets you query historical data as it existed at a previous point in time.
- Use `AT(TIMESTAMP => ...)` to query data at a specific timestamp.
- Use `AT(OFFSET => -N)` to query data as it was N seconds ago.
- Use `BEFORE(STATEMENT => 'query_id')` to see data before a specific query ran.
- `DATA_RETENTION_TIME_IN_DAYS` controls how far back Time Travel can go (default: 1 day, max: 90 for Enterprise edition).
- Time Travel is invaluable for recovering from accidental UPDATEs or DELETEs.
- The `SET variable = value` syntax stores session variables; reference them with `$variable`.

### Permanent Table
**Permanent tables** (default) support Time Travel up to 90 days and have Fail-safe (7 days of additional recovery by Snowflake support).

Snowflake allows an addional 7 day FAIL_SAFE after the retention period is over, however inorder to recover data after the failsafe period is over we need to contact Snowflake support. FAIL_SAFE only applies to permanent tables.

### Transient Table
**Transient tables** (`CREATE TRANSIENT TABLE`) have Time Travel limited to 0-1 days and NO Fail-safe — good for staging/ETL data you can regenerate.
* Transient Tables can have a retention period of 0 or 1 days. 
* These dont have any fail safe.
* Transient tables lasts until they are explicitly dropped.

### Temporary Table
**Temporary tables** (`CREATE TEMPORARY TABLE`) exist only for the session duration, have 0-1 day Time Travel, and no Fail-safe.
* Temporary Tables can have a retention period of 0 or 1 days.
* These dont have any fail safe.
* Temporary tables lasts only during the duration of a session.


Transient and temporary tables cost less storage because they skip Fail-safe.

You cannot set `DATA_RETENTION_TIME_IN_DAYS` higher than 1 for transient or temporary tables.