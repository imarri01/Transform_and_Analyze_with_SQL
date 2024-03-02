
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
[**`/images`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/images) - All non-query result images used in the project
[**`/raw_data`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/raw_data) - This folder contains the CSV source data that was used in this project. The data used for the analytics table exceeds Githubs 100MB file size limit, as a result I had to upload the csv to Google Cloud Storage. The downloaded URL can be found in `analyticss.txt`.
[**`/result_screenshots`**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/tree/main/result_screenshots)
[**QA.md**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/QA.md)
[**README.md**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/README.md)
[**assignment.md**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/assignment.md)
[**cleaning_data.md**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/cleaning_data.md)
[**schema.png**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/schema.png)
[**starting_with_data.md**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/starting_with_data.md)
[**starting_with_questions**](https://github.com/imarri01/lhl-proj-wk6-ramonkidd/blob/main/starting_with_questions.md)


## Process
### (your step 1)
### (your step 2)

## Results
(fill in what you discovered this data could tell you and how you used the data to answer those questions)

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
