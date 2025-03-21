
# Question 1: Are they duplicate visitors in the analytics table?

SQL Queries:

```sql
SELECT FullVisitorId, visitnumber, visitid, COUNT(*) AS duplicate_count
FROM analytics
GROUP BY fullVisitorId, visitId, visitnumber### Second Question: Calculating Bounce Rate  
```


![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.55.48 PM.png)



# Question 2: What is the bounce rate (percentage of visitors who leave after viewing only one page)?


SQL Queries:

```sql
SELECT 
    (COUNT(CASE WHEN pageviews = 1 THEN fullVisitorID END) * 100.0 / COUNT(fullVisitorID)) AS bounce_rate
FROM cleaned_analytics;
```



![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.58.10 PM.png)


# Question 3: How many sessions can we classify as 'bounces' (i.e., sessions with no time spent on the site) across all cities in the dataset?



```sql
SELECT a.city,
       COUNT(ca.fullvisitorid) AS bounce_count
FROM cleaned_analytics ca
JOIN cleaned_all_sessions a ON ca.fullvisitorid = a.fullvisitorid
WHERE (ca.timeonsite = '0' OR ca.bounces = '1') 
GROUP BY a.city
ORDER BY bounce_count DESC;
```



![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.58.50 PM.png)


# Question 4: What are the top traffic sources (organic, paid, direct, referral)?



```sql
SELECT channelgrouping,
       COUNT(DISTINCT fullvisitorid) AS unique_visitors
FROM all_sessions
GROUP BY channelgrouping
ORDER BY unique_visitors DESC;
```



![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 9.59.59 PM.png)

# Question 5: Which product categories receive the most views?



```sql
SELECT productsku,
       COUNT(*) AS view_count
FROM cleaned_all_sessions
WHERE productsku IS NOT NULL
GROUP BY productsku
ORDER BY view_count DESC
LIMIT 10;
```



![Sample Output](/Users/renudinesh/Desktop/Data Science Pictures/Screen Shot 2025-03-06 at 10.01.30 PM.png)
