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



## Cloning and Resource Monitors
### Cloning 
`CLONE` creates a zero-copy clone — it's instant and doesn't duplicate storage until data diverges.
- You can clone databases, schemas, and tables.
- Clones are independent: changes to the clone don't affect the original, and vice versa.
- Cloning is great for creating dev/test environments from production data without extra cost.
- Combine with Time Travel: `CLONE ... AT(TIMESTAMP => ...)` to clone historical data.

### Resource monitors
**Resource monitors** track and control credit usage for warehouses.
- Set `CREDIT_QUOTA` to define the monthly credit limit.
- Use triggers at different thresholds: `NOTIFY` (alert only), `SUSPEND` (stop new queries), `SUSPEND_IMMEDIATELY` (kill running queries).
- Resource monitors can be assigned to one or more warehouses, or to the entire account.
- Only `ACCOUNTADMIN` can create resource monitors by default.
- They reset at the start of each billing cycle (or custom frequency).

### Expanded list of cloneable objects

The video covers cloning tables, schemas, and databases. The set of objects that support cloning has expanded. Tasks and Streams can now be cloned, which is particularly relevant for data engineering workflows. Cloning a schema that contains tables, streams, and tasks creates an isolated copy of an entire data pipeline for testing or development purposes without affecting the production environment.

### Cloning Marketplace shared databases

A common pattern in current Snowflake usage is cloning a shared database obtained from the Snowflake Marketplace. Marketplace datasets are delivered as shared databases, which are read-only by design. To enrich a Marketplace dataset, join it persistently with internal data, or apply custom transformations, the shared database can be cloned to create a local editable copy. The clone is zero-copy at the moment of creation. Changes to the Marketplace source after cloning are not automatically reflected in the cloned copy, and the two will diverge independently from the point of cloning.


## User Defined Functions (UDFs) and User Defined Table Functions(UDTFs)
**UDFs** (User Defined Functions) return a single scalar value per row. **UDTFs** (User Defined Table Functions) return a table.
- UDFs can be written in SQL, JavaScript, Python, or Java.
- Call a scalar UDF in a `SELECT`: `SELECT my_function(column)`.
- Call a UDTF with `TABLE()`: `SELECT * FROM TABLE(my_udtf(args))`.
- Use `CREATE OR REPLACE FUNCTION` to avoid "already exists" errors on re-runs.
- Python UDFs require specifying `LANGUAGE PYTHON`, `RUNTIME_VERSION`, and a `HANDLER` function name.
- UDFs are great for encapsulating reusable business logic that can be shared across queries.


### Snowpark as the recommended Python UDF development pattern

The video demonstrates Python UDFs written as inline strings within a SQL statement. This remains functional but is no longer the recommended approach. The current best practice is to develop Python UDFs using Snowpark in a structured Python environment, like a Snowflake Notebook, a local IDE, or a CI/CD pipeline, where code completion, local testing, and package management are available. Deployment via Snowpark allows Python functions to be referenced directly without embedding them as strings in SQL, which improves maintainability.

### Supported Python runtime versions

Snowflake currently supports Python 3.9, 3.10, 3.11, and 3.12 for UDFs. Support for Python 3.8 has been deprecated. The runtime version should be specified explicitly using the RUNTIME_VERSION parameter when creating a Python UDF.

## Section: Stored Procedures
**Stored procedures** can perform DDL/DML operations (CREATE, INSERT, UPDATE, DELETE) — unlike UDFs which are read-only.
- Procedures are called with `CALL procedure_name(args)`.
- They can be written in SQL (Snowflake Scripting), JavaScript, Python, or Java.
- Use procedures for administrative tasks, data transformations, or complex multi-step workflows.
- Procedures can run with the caller's rights (`EXECUTE AS CALLER`) or the owner's rights (`EXECUTE AS OWNER`).
- Unlike UDFs, stored procedures cannot be used inside a `SELECT` statement.


## Role-based Access Control
**RBAC** (Role-Based Access Control) is Snowflake's security model — all permissions are granted to roles, and roles are granted to users.
- Use `CREATE ROLE` to define a new role. Use `GRANT` to assign privileges to that role.
- The built-in role hierarchy: `ACCOUNTADMIN` > `SECURITYADMIN` > `USERADMIN` > `SYSADMIN` > `PUBLIC`.
- `SHOW GRANTS TO ROLE role_name` reveals what privileges a role currently holds.
- `SHOW GRANTS TO user user_name` reveals what privileges a user currently holds.
- A user must be granted a role before they can `USE ROLE role_name`.
- Follow the principle of least privilege — grant only the permissions needed for each role's function.
- Use `GRANT ROLE child_role TO ROLE parent_role` to build custom role hierarchies.


## Snowpark Dataframes and VsCode Extensions

### Snowpark Dataframes
- **Snowpark** lets you write Python (or Scala/Java) that executes inside Snowflake — data never leaves the platform.
- A `DataFrame` is a lazy reference to a table or query; operations are not executed until you call `.show()`, `.collect()`, or `.count()`.
- Use `session.table("db.schema.table")` to create a DataFrame from an existing table.
- Chain `.filter()`, `.select()`, `.group_by()`, and `.agg()` for transformations — similar to PySpark or pandas.
- `session.sql("SELECT ...")` returns a DataFrame from raw SQL if you prefer writing SQL directly.
- In Snowflake Notebooks, use `get_active_session()` instead of building a session manually with credentials.



### Snowpark DataFrames - Run Locally
This code can only be run locally after performing the install with the above command in your local terminal or shell
**Tips:**
- When connecting from a local environment, you need a `Session` object configured with your account, user, password, role, warehouse, database, and schema.
- Keep credentials in a separate file (e.g., `credential.py`) and add it to `.gitignore` to avoid committing secrets.
- The VS Code Snowflake extension provides IntelliSense, syntax highlighting, and the ability to run queries directly from the editor.

#### Shell Install Command For Snowpark Python Library


`pip install snowflake-snowpark-python`

**Tip:** This install is only needed when running Snowpark outside of Snowflake (e.g., in a local IDE). In Snowflake Notebooks, Snowpark is pre-installed and ready to use.


```
# Import the Snowpark Session class for connecting to Snowflake
from snowflake.snowpark import Session
import os

# Connection parameters – same pattern as above
# In production, use secrets management rather than environment variables
connection_params = {
    "account": os.environ["SNOWFLAKE_ACCOUNT"],
    "user": os.environ["SNOWFLAKE_USER"],
    "password": os.environ["SNOWFLAKE_PASSWORD"],
    "role": "SYSADMIN",
    "warehouse": "COMPUTE_WH",
    "database": "FROSTBYTE_TASTY_BYTES",
    "schema": "RAW_POS"
}

# Establish the Snowpark session
session = Session.builder.configs(connection_params).create()

# Reference the menu table as a Snowpark DataFrame
df = session.table("TASTY_BYTES_SAMPLE_DATA.raw_pos.menu")

# Filter for a specific truck brand and display results
df.filter(col("TRUCK_BRAND_NAME") == "Freezing Point").show()
```