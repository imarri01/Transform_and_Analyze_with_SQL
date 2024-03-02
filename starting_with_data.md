**Question 1: Find all products that are currently out of stock**

SQL Queries:
```sql
SELECT
    DISTINCT(prod_name),
    stocklevel,
    restockingleadtime
FROM
    sales_report
WHERE
    stocklevel = 0
ORDER BY
    prod_name ASC;
```

Answer: \
*77 Products are out of stock*. See sample output below \
![Alt text](/result_screenshots/sec4_q1_result.png)

---

**Question 2: Which product was the most unpopular based on sentiment score**

SQL Queries: 
```sql
SELECT
    prod_name,
    count(sentimentscore),
    sentimentscore
FROM
    sales_report
WHERE
    sentimentscore < 0
GROUP BY
    prod_name,
    sentimentscore
ORDER BY
    sentimentscore ASC
LIMIT
    1;
```

Answer: *7 Dog Frisbee*


---

**Question 3: What were the 5 most popular shopping days**

SQL Queries:
```sql
SELECT
    COUNT(visit_date),
    visit_date
FROM
    analytics_table
GROUP BY
    visit_date
ORDER BY
    COUNT(visit_date) DESC
LIMIT
    5;
```

Answer: 
*The 17th, 18th and 24th of May and the 18th and 31st of August.*

**Question 4: Rank product popularity based on sentiment score**

SQL Queries:
```sql
/* Rank product popularity based on sentiment score
Here we used a case statement, similar to an if then else condition
to classify the respective products based on customer sentimentscore*/
SELECT
    *,
    CASE
        WHEN sentimentscore >= 0.8 AND sentimentscore <= 1.0 THEN 'Very popular'
        WHEN sentimentscore >= 0.6 AND sentimentscore < 0.8 THEN 'Liked'
        WHEN sentimentscore >= 0 AND sentimentscore < 0.6 THEN 'Not so popular'
        WHEN sentimentscore < 0 THEN 'Unpopular'
    END AS popularity
FROM
    products;
```

Answer: \
![Alt text](/result_screenshots/popularity.png)

---
**Question 5: Find all products where the total order is over 100 units and of those products, find the top 10 and determine which product is the most popular**

SQL Queries:
```sql
WITH product_popularity AS (
    SELECT
        *,
        CASE
            WHEN sentimentscore >= 0.8 AND sentimentscore <= 1.0 THEN 'Very popular'
            WHEN sentimentscore >= 0.6 AND sentimentscore < 0.8 THEN 'Liked'
            WHEN sentimentscore >= 0 AND sentimentscore < 0.6 THEN 'Not so popular'
            WHEN sentimentscore < 0 THEN 'Unpopular'
        END AS popularity
    FROM
        products
)
SELECT
    SUM(sbs.total_ordered) as total_cust_orders,
    product_popularity.productsku,
    product_popularity.product_name,
    product_popularity.popularity
FROM
    product_popularity
    JOIN sales_by_sku sbs ON sbs.productsku = product_popularity.productsku
WHERE
    product_popularity.popularity = 'Very popular'
GROUP BY
    product_popularity.productsku,
    product_popularity.product_name,
    product_popularity.popularity
HAVING
    SUM(sbs.total_ordered) > 100
ORDER BY
    SUM(sbs.total_ordered) DESC
LIMIT
    10;
```

Answer: *17 oz Stainless Steel Sport Bottle*


