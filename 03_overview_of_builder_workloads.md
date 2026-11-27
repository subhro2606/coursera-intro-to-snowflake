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

# GenAI Overview
Note: This code will not work in a trial account. You must use a paid, non-trial account to use the Cortex Search Service


## Cortex Search 
**Cortex Search** provides a managed RAG (Retrieval-Augmented Generation) service — it indexes your text data and lets you query it with natural language.
- It automatically handles chunking, embedding, and vector search behind the scenes.
- Use it when you need semantic search over documents, support tickets, knowledge bases, etc.
- Cortex Search works with structured text columns in Snowflake tables — no need to manage external vector databases.

### Lesson: Cortex CLI

**Tips:**
- The `snow cortex` CLI commands let you use LLM functions directly from your terminal.
- `snow cortex complete` sends a prompt to a model and returns the completion.
- `snow cortex translate` translates text between languages.
- You can pipe text from files with `cat file.txt | snow cortex complete "prompt"` for batch processing.
- Chain commands together to build multi-step AI pipelines (e.g., summarize then translate).
- These commands run against your Snowflake account — the data stays within Snowflake's security boundary.

## Cortex CLI

```
1) snow --version

2) snow cortex --help

3) snow cortex complete "What are three reasons Snowflake is well suited for AI workloads?"

4) snow cortex complete "What are three reasons Snowflake is well suited for AI workloads?" 
  --model snowflake-arctic

5) cat sample_review.txt

6) cat sample_review.txt | snow cortex complete \
  "Summarize this review in one sentence and classify the sentiment:" 
  --model snowflake-arctic

7) result=$(snow cortex complete \
  "Give me a one-word sentiment: positive, negative, or neutral. Review: Great product, fast delivery." \
  --model snowflake-arctic)
echo "Sentiment was: $result"
```



## Cortex LLM Functions
**Cortex LLM Functions** are SQL-native AI functions you call directly in queries: `SNOWFLAKE.CORTEX.COMPLETE()`, `SNOWFLAKE.CORTEX.SENTIMENT()`, `SNOWFLAKE.CORTEX.SUMMARIZE()`, `SNOWFLAKE.CORTEX.TRANSLATE()`, etc.
- They process data row-by-row, so you can apply AI to every row in a table with a simple SELECT.
- No model deployment or infrastructure management needed — just call the function.
- `COMPLETE()` takes a model name and a prompt, and returns the generated text.
- These functions keep your data inside Snowflake's governance perimeter — data never leaves your account.

### Additional COMPLETE parameters
AI_COMPLETE now supports parameters beyond model name and prompt. Three are worth noting:
* **temperature** controls the determinism of the output. A value of 0 produces consistent, repeatable responses. Higher values introduce more variation.
* **guardrails** enables Snowflake's built-in content safety filtering on the model's response.
* **response_format** allows the model to return structured JSON output, which is useful when the response needs to be parsed programmatically.

### Additional Cortex functions

The videos cover `AI_COMPLETE, AI_SENTIMENT, AI_TRANSLATE, and AI_EXTRACT`. The platform now also includes `AI_PARSE_DOCUMENT` for extracting structured content from files such as PDFs, and `AI_CLASSIFY` for categorizing text into caller-defined groups. Cortex Search and Cortex Analyst, which provide semantic search and natural language SQL capabilities respectively, are covered elsewhere in this module.

### The SUMMARIZE function being deprecated
The videos use the SUMMARIZE function in a key demonstration. SUMMARIZE is deprecated and will eventually be removed. The recommended replacement is to use AI_COMPLETE with an explicit summarization instruction in the prompt:



# Snowflake Apps

## Cortex functions in application backends

The video covers connecting applications to Snowflake through drivers and connectors to execute SQL queries. Now, a common pattern in application development is to embed Cortex AI functions within that same backend logic. An application backend that connects to Snowflake via the Python connector can call AI_COMPLETE, Cortex Search, or Cortex Analyst as part of its query execution, returning AI-enhanced results to the application's frontend without requiring separate AI infrastructure. Because Cortex functions execute within Snowflake's governed environment, the same access controls and data policies that apply to standard queries apply to AI-enabled queries. Cortex functions are covered in detail in the remaining videos of this module.

## Hybrid Tables

Hybrid Tables have matured significantly since the video was recorded. Primary key and unique constraints are now enforced, indexes are supported for fast single-row lookups, and the operational characteristics for transactional workloads are more clearly documented. Hybrid Tables are appropriate for applications requiring concurrent read and write operations on individual rows alongside analytical queries against the same data, eliminating the need to maintain separate transactional and analytical database systems.

## The Snowflake REST API

The video references the Snowflake Python API for programmatic object management. Snowflake also provides a REST API for executing SQL and managing resources. The REST API is appropriate for integration scenarios where installing a full driver or connector is not practical, including serverless functions, lightweight webhook-driven automations, or environments with restricted dependency management.


# Applications with Snowflake
**Streamlit in Snowflake (SiS)** lets you build interactive data apps directly in Snowsight — no separate hosting needed.
- Create a Streamlit app from the Snowsight UI: Projects > Streamlit > + Streamlit App.
- Your app runs inside Snowflake, so it has direct access to your data with full governance and security.
- Common components: `st.dataframe()` for tables, `st.bar_chart()` for charts, `st.selectbox()` for filters, `st.text_input()` for user input.
- Use `get_active_session()` inside your Streamlit app to query Snowflake data.
- Apps can be shared with other Snowflake users via role-based access.