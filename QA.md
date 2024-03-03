What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

Have a file or table that has all the standards for productsku, visitor id and so on that you can use as single source of truth for schema, datatypes and record values.

#### QA Approach
---
##### Approach 1

Before I go into the SQL specific data quality stuff that was specifically asked in the question, I created a separate process using a number of Google Cloud services, to make it closer and more relatable to my day to day work. I wanted to push myself a bit to see if I could create a somewhat automated QA/Data Quality process. The process is outline below, along with the respective results.

###### Architecture

![Alt text](/data_quality/qa_architecture.png)


**Step 1:**
- Load CSV data in Google Cloud Storage.
Google Cloud Storage acted a data lake. The 5 CSV files were uploaded to a Google Cloud Storage bucket in their raw format. See screenshot below. 

![Alt text](/data_quality/raw_files_gcs.png)

**Step 2:**
- Import CSV files into BigQuery to create tables in the ecommerce dataset.
Note: Using BigQuery for this specific project is somewhat of an overkill, however its an excellent OLAP database so I wanted to get more hands on with it versus AlloyDB or another relational database service. 

![Alt text](/data_quality/bq_tables.png)


**Step 3:**
- Define data quality job
Here is the most critical piece to the process. This is where we define our QA rules. In this example I checked for `NULL` values in the userid field in the `analytics` tbale. I also check if the `productSKU` in the `all_sessions` adhere to the format defined `GGOEG`. See yaml file that was used to define these rules. These might seem like overkill for this small project but I like to think about scale and the below file and rules can be expanded to multiple columns spanning across several tables, indicating how many records break our data quality rules/definition while flagging. See rules definition below.

```yaml
metadata_registry_defaults:
  dataplex:
    projects: lhl-wk6-project
    locations: northamerica-northeast2
    lakes: ecommerce-lake
    zones: user-activity-raw-zone
row_filters:
  NONE:
    filter_sql_expr: |-
      True
rule_dimensions:
  - consistency
  - correctness
  - duplication
  - completeness
  - conformance
  - integrity
  - timeliness
  - accuracy
rules:
  NOT_NULL:
    rule_type: NOT_NULL
    dimension: completeness
  VALID_SKU:
    rule_type: REGEX
    dimension: conformance
    params:
      pattern: |-
        ^GGOEG*
rule_bindings:
  VALID_CUSTOMER:
    entity_uri: bigquery://projects/lhl-wk6-project/datasets/lhl_ecommerce/tables/analytics
    column_id: userid
    row_filter_id: NONE
    rule_ids:
      - NOT_NULL
  VALID_SKU_ID:
    entity_uri: bigquery://projects/lhl-wk6-project/datasets/lhl_ecommerce/tables/all_sessions
    column_id: productSKU
    row_filter_id: NONE
    rule_ids:
      - VALID_SKU
```

**Step 4:**
- Execute Data Quality Job
These rules are then loaded in a batch pySpark job to run again the datasets created in Step 2.

Step 5
- The results are then written to another BigQuery dataset, with the results of our data quality job.

![Alt text](/data_quality/dq_results.png)

The below query was executed against our `dq_summary` table. The JSON result is shown after the SQL, indicating that we have `376` of the total `15134` records in the `all_sessions` table that have productSKUs that failed our quality checks. It also shows that all `userid` in the `analytics` table contain `NULL` values.

```sql
SELECT
  rule_binding_id,
  rule_id,
  table_id,
  column_id,
  dimension,
  rows_validated,
  success_count,
  failed_count
FROM
  `lhl-wk6-project.lhl_ecommerce_dq_results.dq_summary`
WHERE
  invocation_id = '2870c1c4-2eef-494f-86b9-2deb786b5ee9'
```

RESULTS

```json
[{
  "rule_binding_id": "VALID_SKU_ID",
  "rule_id": "VALID_SKU",
  "table_id": "lhl-wk6-project.lhl_ecommerce.all_sessions",
  "column_id": "productSKU",
  "dimension": "CONFORMANCE",
  "rows_validated": "15134",
  "success_count": "14758",
  "failed_count": "376"
}, {
  "rule_binding_id": "VALID_CUSTOMER",
  "rule_id": "NOT_NULL",
  "table_id": "lhl-wk6-project.lhl_ecommerce.analytics",
  "column_id": "userid",
  "dimension": "COMPLETENESS",
  "rows_validated": "4301122",
  "success_count": "0",
  "failed_count": "4301122"
}]
```
---
##### Approach 2
