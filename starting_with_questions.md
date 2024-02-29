Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```sql
/* data discovery, trying to determine which column to use;
 totaltransactionsrevenue or transactionrevenue */
SELECT totaltransactionrevenue, transactionrevenue, country, city 
FROM all_sessions;

-- removing null values
SELECT totaltransactionrevenue, country, city
FROM all_sessions 
WHERE totaltransactionrevenue IS NOT NULL;

-- getting the sum of total transactions revenue per city per country
SELECT sum(totaltransactionrevenue), country, city 
FROM all_sessions where totaltransactionrevenue 
IS NOT NULL GROUP BY country, city;

/*getting the sum of total transactions revenue per city per country 
group by country then city and ordered by sum of totaltransactionsrevenue
*/
SELECT sum(totaltransactionrevenue), country, city 
FROM all_sessions 
WHERE totaltransactionrevenue IS NOT NULL 
GROUP BY country, city
ORDER BY sum(totaltransactionrevenue) DESC;
```



Answer: *Country - United States; City - San Francisco*




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
``` sql
/* data gathering to see which tables make sense to answer this question */
SELECT * FROM sales_by_sku;
SELECT * FROM all_sessions;

/*order by city and country
Here I'm looking at the sales by sku table to get an idea of all the sales by SKU then getting
the country and city from the sessions table
*/
SELECT COUNT(s_sku.total_ordered) AS total_products_ordered, country, city
FROM all_sessions a_sess
JOIN sales_by_sku s_sku ON a_sess.productsku = s_sku.productsku
GROUP BY country, city
ORDER BY country DESC;

/* Lets now get the average number of products in each country and city */
SELECT ROUND(AVG(s_sku.total_ordered), 2) AS total_products_ordered, country, city
FROM all_sessions a_sess
JOIN sales_by_sku s_sku ON a_sess.productsku = s_sku.productsku
GROUP BY country, city
ORDER BY country DESC
```



Answer: *query returned 388 rows*





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
``` sql
/* identifying pattern in the products ordered. 
Not using the average this time but using the count of the product skus and updating query to add product name.
The results were ordered by productname to see which products were most popular in each country and city*/
SELECT COUNT(s_sku.productsku) AS productsku, country, city, prod.product_name
FROM all_sessions a_sess
JOIN sales_by_sku s_sku ON a_sess.productsku = s_sku.productsku
JOIN products prod ON prod.sku = s_sku.productsku
WHERE a_sess.city != 'not available in demo dataset' -- removing empty cities
GROUP BY prod.product_name, a_sess.country, a_sess.city
ORDER BY COUNT(s_sku.productsku) DESC

/*removing cities and focusing more on the countries to see if there are any trends.
This just shows most popular products in the countries
*/
SELECT COUNT(s_sku.productsku) AS productsku, country, prod.product_name
FROM all_sessions a_sess
JOIN sales_by_sku s_sku ON a_sess.productsku = s_sku.productsku
JOIN products prod ON prod.sku = s_sku.productsku
GROUP BY prod.product_name, a_sess.country
ORDER BY COUNT(s_sku.productsku) DESC
```



**Answer**: *Mountain View United States had a lot of home security camera orders and smoke and CO alarms along with twill caps. United Kingdom also had popular twill cap orders. Popular products - US - "Men's 100% cutton short sleeve hero tee white"*





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







