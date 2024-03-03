What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

Have a file or table that has all the standards for productsku, visitor id and so on that you can use as single source of truth for schema, datatypes and record values.

#### QA Approach

Before I go into the SQL specific data quality stuff that was specifically asked in the question, I created a separate process using a number of Google Cloud services, to make it closer and more relatable to my day to day work. I wanted to push myself a bit to see if I could create a somewhat automated QA/Data Quality process. The process is outline below, along with the respective results.


##### Architecture

![Alt text](/data_quality/qa_architecture.png)


Step 1.
- Load CSV data in Google Cloud Storage.
Google Cloud Storage acted a data lake. The 5 CSV files were uploaded to a Google Cloud Storage bucket in their raw format. See screenshot below. \

![Alt text](/data_quality/raw_files_gcs.png)

