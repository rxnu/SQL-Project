
### What issues will you address by cleaning the data?

1. **Null or Missing Values**:  
   Many columns in the dataset contained null or missing values, which could lead to inaccurate analysis. To handle this, I used the `NULLIF()` and `COALESCE()` functions. In one instance, `COALESCE(timeonsite, '0')` was used to replace any null values in the `timeonsite` column with a default value of `'0'`, making sure that calculations related to session duration are accurate.

2. **Unwanted Placeholder Values**:  
   Some columns, particularly `country` and `city`, contained placeholder values like `(not set)` or `not available in dataset`, which could mislead the analysis or cause errors. Similarly, the `productcategory` column contained placeholder values such as `(not set)` and `${escCatTitle}`. I used the `NULLIF()` function to replace these placeholders with `NULL`, along with the `WHERE` clause in specific queries to filter out invalid values.

3. **Data Type Conversion**:  
   Certain columns, such as `totalTransactionRevenue`, `productRevenue`, `timeOnSite`, and `productQuantity`, contained values that needed to be converted to the correct data type for analysis. The `CAST()` function was used to convert data into the appropriate numeric types, allowing for aggregation and mathematical operations to be performed properly.

4. **Data Aggregation**:  
   Aggregation and scaling were performed where necessary. I divided `totalTransactionRevenue` by 1,000,000 to simplify large revenue figures and make them easier to interpret.

5. **Filtering Invalid or Irrelevant Data**:  
   To remove invalid or irrelevant records, I used the `WHERE` clause to filter out rows with null values in important columns, like `city` and `country`. For example, in some cases, the query `WHERE city AND country IS NOT NULL` ensures only relevant data is included in the analysis.

6. **Removing Duplicates**:  
   Duplicates in the fullvisitorid column were removed using a CTE and ROW_NUMBER(). The query assigned a row number to each fullvisitorid, and rows with rn > 1 (duplicates) were deleted. This later allowed for a primary key to be added to the table. Moreover, the DISTINCT function was applied to remove any duplicates in the tables being used in views, ensuring that only unique records were considered for analysis.
   
### Queries: Below are the SQL queries you used to clean your data.

### Deleting Duplicates from fullvisitorid (primary key)


```sql
WITH Duplicates AS (
    SELECT fullvisitorid,
           ROW_NUMBER() OVER (PARTITION BY fullvisitorid ORDER BY fullvisitorid) AS rn
    FROM all_sessions
)
DELETE FROM all_sessions
WHERE fullvisitorid IN (
    SELECT fullvisitorid
    FROM Duplicates
    WHERE rn > 1
);
```
### Creating View Tables that Cleaned the Data:

```sql
CREATE OR REPLACE VIEW cleaned_all_sessions AS
SELECT DISTINCT
    fullVisitorId, 
    channelGrouping, 
    productsku, 
    v2productname AS productname,
    NULLIF(NULLIF(country, '(not set)'), 'not available in demo dataset') AS country,
    NULLIF(NULLIF(city, '(not set)'), 'not available in demo dataset') AS city,
    totalTransactionRevenue / 1000000 AS totaltransactionrevenue, 
    transactions,
    timeOnSite,
    pageviews,
    productQuantity,
    productRevenue,
    NULLIF(NULLIF(v2productCategory, '${escCatTitle}'), '(not set)') AS productCategory,
    date
FROM All_sessions
WHERE country NOT LIKE '%not%'
    AND city NOT LIKE '%not%';
```

```sql
CREATE OR REPLACE VIEW cleaned_analytics AS 
SELECT DISTINCT 
    fullvisitorid,
    COALESCE(timeonsite, '0') AS timeonsite,  
    pageviews,
    CASE 
        WHEN timeonsite IS NOT NULL THEN '0' 
        WHEN timeonsite IS NULL THEN '1'      
    END AS bounces
FROM analytics
WHERE fullvisitorid IS NOT NULL;
```

### Data Filtering 

```sql
WHERE totaltransactionrevenue IS NOT NULL 
  AND city IS NOT NULL 
  AND country IS NOT NULL;
```

```sql
WHERE productCategory IS NOT NULL 
  AND city IS NOT NULL 
  AND country IS NOT NULL
  AND productQuantity IS NOT NULL;
```



