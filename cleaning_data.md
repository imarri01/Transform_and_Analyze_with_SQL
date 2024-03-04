### Question: What issues will you address by cleaning the data?
When cleaning raw data as given in this project, several common issues are typically addressed to ensure the data is accurate, consistent, and usable for analysis or modeling. These issues include:

1. **Missing Values:** Identifying and handling missing data, which might involve filling in missing values with a suitable default or removing records with missing data.

2. **Duplicate Records:** Detecting and removing duplicate entries to prevent skewed analysis results.

3. **Inconsistent Data:** Standardizing data formats and correcting inconsistencies, such as varying date formats, mixed capitalization, or different naming conventions. See an example in QA where productSKU standard was verified.

4. **Outliers:** Identifying and addressing extreme values that may be errors or anomalies, which can distort statistical analyses.

5. **Data Type Mismatches:** Ensuring that each column's data type is appropriate for its content, such as converting strings to numeric types or dates to date-time formats.

6. **Erroneous Values:** Identifying and correcting data entry errors or implausible values, such as negative ages or impossible coordinates.

7. **Handling Text Data:** Cleaning and preprocessing text data, including removing unnecessary characters.

### Queries:
Due to time constraint and with me leaving this section as the last section I wrote up, not all these queries were executed to clean the data before answering the questions, only a few were executed but I find it useful to include some of the queries can be used to address the above; to be used in the future.


***Filling Missing values***
```sql
/* Replace NULL values with a default value (e.g., 0 for a numeric column) */
UPDATE analytics_able
SET userid = COALESCE(userid, 0);
```

***Convert data type using correct format***

```sql
/* Convert sess_date data type from integer to date format */
ALTER TABLE
    all_sessions
ALTER COLUMN
    sess_date TYPE DATE USING to_date(to_char(sess_date, '99999999'), 'YYYYMMDD');

/* verify change was made */
SELECT
    column_name,
    data_type
FROM
    information_schema.columns
WHERE
    table_name = 'all_sessions'
    and column_name = 'sess_date'
```


***Cleaning text data***

```sql
/*  Remove unnecessary characters from a text column */
UPDATE all_sessions
SET v2productname = REGEXP_REPLACE(v2productname, '[^a-zA-Z0-9\s]', '', 'g');

```