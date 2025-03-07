

# What are your risk areas? Identify and describe them.

1. **Mismatched Records Between Key Tables**:  
   One of the biggest risks in the data is mismatched records between key tables. If `fullVisitorId` and `visitId` don’t align between `All_sessions` and `analytics`, we could end up with missing or incomplete data. A session recorded in one table but not the other might indicate tracking gaps, integration errors, or lost records—all of which could skew our analysis.

2. **Inconsistent Product Data**:  
   Similarly, inconsistencies in `productSku` across `All_sessions`, `sales_report`, and `sales_by_sku` can make product-level analysis unreliable. If sales data isn’t properly linked across these tables, it could lead to inaccurate revenue calculations and misleading business insights.

3. **Duplicate Entries**:  
   Duplicate entries are another major concern. If the same `fullVisitorId`  appear multiple times, session counts could be inflated, and theryby throw off key performance metrics. 

4. **Discrepancies in Sales Data**:  
   Lastly, discrepancies in `total_ordered` values between `sales_report` and `sales_by_sku` present a risk. If the total number of products sold doesn’t match across these reports, it could signal missing transactions or aggregation errors.

** Due to numerous discrepancies between tables, many of the queries were designed to use columns from only `All_sessions` or `analytics` to minimize the chance of errors. For example, I used `productquantity` consistently when it make to finding details on product stock as opposed to the other tables (`sales_by_sku` and `sales_report`) to make the process more simple and streamlined. **

# QA Process:  
Describe your QA process and include the SQL queries used to execute it.

# 1. **Identifying Missing Records**:

## To find sessions from `analytics` that don’t exist in `All_sessions`:

```sql
SELECT t.fullVisitorId, t.visitId
FROM analytics t
LEFT JOIN All_sessions a 
ON t.fullVisitorId = a.fullVisitorId 
AND t.visitId = a.visitId
WHERE a.fullVisitorId IS NULL;
```

- This query identifies 4200399 sessions from analytics that don't exist in All_sessions.

## To find sessions from All_sessions that don’t exist in analytics:

```sql
SELECT a.fullVisitorId, a.visitId
FROM All_sessions a
LEFT JOIN analytics t 
ON a.fullVisitorId = t.fullVisitorId 
AND a.visitId = t.visitId
WHERE t.fullVisitorId IS NULL;
```
- This query identifies 11,375 visitor sessions from All_sessions with no matching entry in analytics.

# **2. Counting Total Sessions:**

## To compare the total number of sessions in both All_sessions and analytics:

```sql
SELECT 
    (SELECT COUNT(*) FROM All_sessions) AS all_sessions_count,
    (SELECT COUNT(*) FROM analytics) AS analytics_count;
```

- This query reveals a large difference in sessions between the two tables (13429 in all_sessions and 1444 in analytics)

# **3. Analyzing for Duplicates:**

## To identify duplicate entries in All_sessions:

```sql
SELECT fullVisitorId,  COUNT(*) 
FROM All_sessions 
GROUP BY fullVisitorId, 
HAVING COUNT(*) > 1;
```

- This query identifies duplicates in All_sessions prior to data cleaning. 

## To identify duplicate entries in analytics:

```sql
SELECT fullVisitorId,  COUNT(*) 
FROM analytics 
GROUP BY fullVisitorId, 
HAVING COUNT(*) > 1;
```

- This query identifies duplicates in analytics prior to data cleaning. 

## To check if duplicated data (fullvisitorid) had been removed post data cleaning.  

```sql
SELECT fullvisitorid, COUNT(*)
FROM all_sessions
GROUP BY fullvisitorid
HAVING COUNT(*) > 1;
```

```sql
SELECT fullvisitorid, COUNT(*)
FROM analytics
GROUP BY fullvisitorid
HAVING COUNT(*) > 1;
```
- Yielded 0 results for both.


# **4. Product Data Validation:**

## To find productsku values in All_sessions that don’t exist in Product_details:

```sql
SELECT a.productsku
FROM All_sessions a
LEFT JOIN products p ON a.productsku = p.sku
WHERE p.sku IS NULL;
```

- This query identifies missing productsku values in All_sessions.

## To find missing productsku values in sales_report:

```sql
SELECT s.productsku
FROM sales_report sr
LEFT JOIN all_sessions s ON s.productsku = sr.productsku
WHERE s.productsku IS NULL;
```

- This query identifies 176 missing values in sales_report.

## To find product SKUs in sales_by_sku that do not exist in sales_report:

```sql
SELECT sbs.productsku, sbs.total_ordered
FROM sales_by_sku sbs
LEFT JOIN sales_report sr ON sbs.productsku = sr.productsku
WHERE sr.productsku IS NULL;
```

- This query checks for discrepancies in productsku between sales_by_sku and sales_report. (8 total)

# **5. Checking Aggregated Data:**

## To check for discrepancies in total_ordered values between sales_by_sku and sales_report:

```sql
SELECT productsku, SUM(total_ordered) 
FROM sales_by_sku  
GROUP BY productsku 
ORDER BY SUM(total_ordered) DESC;
```

- This query yields 462 rows from sales_by_sku.

```sql
SELECT productsku, SUM(total_ordered) 
FROM sales_report
GROUP BY productsku 
ORDER BY SUM(total_ordered) DESC;
```

- This query yields 454 rows from sales_report. This indicates a mismatch in the total_ordered column between the two tables.





