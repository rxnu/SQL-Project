Answer the following questions and provide the SQL queries used to find the answer.

    
### Question 1: Which cities and countries have the highest level of transaction revenues on the site?

#### SQL Query:

```sql
SELECT country, city, SUM(totaltransactionrevenue) AS total
FROM cleaned_all_sessions
WHERE totaltransactionrevenue IS NOT NULL 
  AND city IS NOT NULL 
  AND country IS NOT NULL
GROUP BY country, city
ORDER BY total DESC;
```

Answer:

![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/first question.png)



### Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

```sql
SELECT DISTINCT 
    country, 
    city, 
    AVG(productquantity) AS average_ordered_quantity
FROM cleaned_all_sessions
WHERE productquantity IS NOT NULL 
  AND city IS NOT NULL 
  AND country IS NOT NULL
GROUP BY country, city
ORDER BY average_ordered_quantity DESC;
```


Answer:

![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.38.30 PM.png)




**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

```sql
SELECT country, city, productcategory, SUM(productQuantity) AS total_quantity_ordered
FROM cleaned_all_sessions
WHERE productCategory IS NOT NULL 
  AND city IS NOT NULL 
  AND country IS NOT NULL
  AND productQuantity IS NOT NULL
GROUP BY country, city, productCategory
ORDER BY total_quantity_ordered DESC;
```

Answer: 

![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.39.59 PM.png)

## **Product Category Distribution Across Cities and Countries**

```sql
SELECT 
    productcategory,
    COUNT(DISTINCT city) AS num_cities,
    COUNT(DISTINCT country) AS num_countries,
    SUM(productquantity) AS total_ordered
FROM cleaned_all_sessions
WHERE productcategory IS NOT NULL and productquantity is not null
GROUP BY productcategory
ORDER BY total_ordered DESC;
```

Answer:

![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.41.08 PM.png)



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:


```sql
WITH RankedProducts AS (
    SELECT country, city, productSKU, productname, SUM(productQuantity) AS total_quantity_ordered,
           RANK() OVER (PARTITION BY country, city ORDER BY SUM(productQuantity) DESC) AS rank_number
    FROM cleaned_all_sessions
    WHERE productSKU IS NOT NULL 
      AND productQuantity IS NOT NULL
      AND city IS NOT NULL 
      AND country IS NOT NULL
    GROUP BY country, city, productSKU, productname
)
SELECT *
FROM Rankedproducts
WHERE rank_number = 1
ORDER BY total_quantity_ordered desc
```

Answer:

![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.44.50 PM.png)




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```sql
SELECT country, city, 
     SUM (totaltransactionrevenue) AS total_revenue,
	   AVG (totaltransactionrevenue) as avg_revenue,
	   COUNT(DISTINCT fullvisitorid) AS unique_visitors, 
FROM cleaned_all_sessions
WHERE totaltransactionrevenue IS NOT NULL 
GROUP BY country, city
ORDER BY total_revenue DESC;
```

Answer:


![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.50.43 PM.png)





