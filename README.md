
![Alt text](/images/project_header.png)

## Project/Goals
 Welcome to my Transforming and Analyzing Data with SQL project! This is the first project in the Lighthouse Labs Data Science bootcamp, at the end of the SQL instruction.
 This project offers a chance for me to integrate all the SQL skills I have acquired so far.

#### Project Overview

In this project, I will amalgamate and apply the knowledge I have gained throughout this course, which includes:

- Retrieving data from a SQL database
- Refining, modifying, and examining data
- Inserting data into a database
- Crafting and executing a QA strategy to verify transformed data in comparison to original data


#### Structure of Repository
[**`/images`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/images) - All non-query result images used in the project \
[**`/raw_data`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/raw_data) - This folder contains the CSV source data that was used in this project. The data used for the analytics table exceeds Githubs 100MB file size limit, as a result I had to upload the csv to Google Cloud Storage. The downloaded URL can be found in `analyticss.txt`. \
[**`/result_screenshots`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/result_screenshots) - Screenshots from query results executed in PgAdmin \
[**`QA.md`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/QA.md) - QA method/framework and queries used. \
[**`README.md`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/README.md) - Everything you need to know about the project. This is the file you are currently reading. \
[**`assignment.md`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/assignment.md) - Project Instructions \
[**`cleaning_data.md`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/cleaning_data.md) - Things I kept in mind while trying to clean the data along with queries used \
[**`schema.png`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/schema.png) - The final ERD schema showing the respective between the tables. \
[**`starting_with_data.md`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/starting_with_data.md) - Additional questions I wrote that could be answered using the dataset given. Also contained in this file are the queries I used to answer said questions. \
[**`starting_with_questions`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/starting_with_questions.md) - Questions asked by the project creators, along with the queries used to answer said questions. 


## Process

Below are is the process taken to complete this project:
1. Started out my importing the data
2. Doing some amount of exploratory data analysis by running multiple `select * <table>` on the imported data to quickly determine tables columns with null values that should be ignored.
3. Started with `starting_with_questions` then took note of all the roadblocks experienced in answering the questions and respectuve workarounds.
4. Move on to starting with data and created 5 other questions that could be answered from the data without much cleaning. I avoided questions requiring computations around date and userids. The userid column was filled with null values and while I could easily cast or convert the date types, I didnt trust the data that was given.
5. Based on my experience with #2, #3 and #4, I came up with a couple queries that I used or could be used as part of the QA process to clean up the data. I was on the fence about creating new tables or views, so I just used CTEs where needed to help answer the respective questions without having to create a new table, temp table, a view or updated the existing dataset.
6. Using the existing tables, I ran queries similar to the one below, to identify duplicate values in the respective columns to know which column I could use a primary key or foreign key to build the ERD.
```sql
SELECT
    count(productsku),
    productsku
FROM
    sales_report
GROUP BY
    productsku
HAVING
    count(productsku) > 1;
```
7. I also ran sub-queries similar to the one below to identify the percentage of null values in columns I had concerns about from `step #2` above. If the null percentage was greater than `60` I would not use that column in my queries.
```sql
/* QA check null percentage in columns and if it is 0 where its all nulls or if the percentage */
SELECT 
    userid,
    COALESCE(((total_rec - null_count)::float / total_rec * 100), 0) AS null_percentage
FROM 
    (SELECT 
        userid, 
        COUNT(*) AS total_rec,
        COUNT(userid) AS null_count 
    FROM 
        analytics_table
    GROUP BY 
        userid) AS subquery;
```

## Results

Surprisingly the data, even though it clearly needs a lot of cleaning was able to answer most of the questions I wanted to address. `Step 6` and and `Step 7` from above was very critical. With careful crafting of CTEs and CASE statements, along with choosing the appropriate field help me in answering the respective questions. Accuracy of the query results is questionable in my opinion due to the nature of the data however, they are as accurate as possible given the assumptions used and the state of the tables.

## Challenges 
(discuss challenges you faced in the project)
##### Building the ERD
Finding a suitable primary key for all_sessions and analytics_table was challenging. There was no field that was unique to either table containing no NULL value. The userid column was primarily NULL values so this was useless and I eventually dropped it from the table. I was able to find 2 foreign keys I could use, which I verified via a basic `INNER JOIN`, see example query below.
```sql
SELECT * FROM analytics_table atab JOIN all_sessions asess ON atab.visitid = asess.visitid
SELECT * FROM analytics_table atab JOIN all_sessions asess ON atab.fullvisitorid = asess.fullvisitorid
```
These queries were just used to verify that there is some relationship between the tables and could be use for querying/linking both tables. Another type of join could be used depending on the use case and question we are trying to answer.

##### Answering questions in `starting_with_questions.md` section
1. Had to ignore columns with NULL values and ensure that any window function and or aggregation took this into cconsideration.

## Future Goals


There are several things that come to mind as things I would love to do with more time: Improved data profiling, data standardization, advanced cleaning, performance optimization and data enrichment.

- ***Better data profiling*** - Conduct an even more thorough analysis of the data to understand its structure, content, and quality. This includes identifying patterns, anomalies, missing values, and data distribution. Find the data owners, or the application owners responsible for generating the data, have them implement better data collection and data integrity methods.
- ***Data Standardization*** - Ensure that data across different CSV files follows a consistent format, especially for fields like dates, currencies, and categorical variables. This helps in integrating and comparing data seamlessly.
- ***Data Enrichment*** - Look for ways to add more information to the data, like adding location details, outside of country and state, calculating totals or averages independent of SQL, or using extra data from other sources to make the analysis better.
- ***Advanced Cleaning*** - Beyond basic cleaning, apply more sophisticated techniques to handle outliers, correct errors, and impute missing values, possibly using statistical methods, machine learning algorithms or Python.
- ***Performance Optimization:*** - Relook how the data is normalized. Some of the queries executed took a while to execute, not ideal for a production environment.
