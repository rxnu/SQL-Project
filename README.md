# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals

In this project, I analyzed e-commerce transaction data to uncover insights about sales performance, customer behavior, and product trends. My primary goals were to:

- Identify the cities and countries with the highest transaction revenues.
- Determine the average number of products ordered per visitor in different regions.
- Discover patterns in product categories purchased across various locations.
- Identify top-selling products in each city/country and analyze purchasing trends.
- Summarize regional revenue impacts.
- Investigate user engagement through bounce rates and traffic sources.

## Process

### Step 1: Loading and Preparing the Data

The data was initially imported as text. To ensure proper analysis, I began by converting the fields into their correct data types. Given the size of the dataset, I created view tables for the `analytics` and `all_sessions` datasets, which contained the most relevant data for my analysis. This approach ensured a cleaner, more efficient version of the data for further steps.

### Step 2: Data Cleaning and Preprocessing

Before diving into the analysis, I conducted an initial review of the dataset for inconsistencies, missing values, and incorrect data types. My key checks included:

- Understanding the context of the data to ensure all fields were relevant for the analysis.
- Identifying and handling null or missing values in key fields like transaction revenue, product quantity, and session duration.
- Verifying the data types to ensure they were correctly formatted for analysis.
- Checking for duplicate records and inconsistencies in product names.
- Ensuring key columns were complete and deciding if any data needed to be filtered out (e.g., incomplete sessions or invalid transactions).

### Step 3: Data Cleaning

After reviewing the dataset, I carried out several cleaning steps:

- **Creating Views for Cleaned Data:** I created view tables for the `analytics` and `all_sessions` datasets. This helped me work with a cleaner, more reliable version of the data, simplifying queries and ensuring accurate analysis.
- **Converting Data Types:** I converted the transaction revenue and product quantity fields from text to numeric format, ensuring that calculations were accurate.
- **Removing Duplicates:** Duplicate records were filtered out to maintain data integrity and avoid skewing the results.
- **Filtering Inconsistent Data:** I filtered out incomplete or unreliable session data (e.g., null values or placeholders) to maintain the quality of the analysis.

### Step 4: Data Analysis and Insights

**Transaction and Revenue Analysis**

- Calculated total transaction revenue by country and city, ranking regions by revenue.
- Grouped sales data by product SKU and summed quantities sold to identify top-selling products.
- Calculated the average number of products ordered per visitor in each region.

**Customer Behavior Analysis**

- Calculated bounce rates by determining the percentage of visitors who left after viewing only one page.
- Classified sessions as "bounces" (sessions with no engagement or marked as bounces) to assess user behavior across cities.
- Analyzed traffic sources to identify visitor origins: organic, paid, direct, or referral.

**Product Category and Sales Trends**

- Explored purchasing trends by product category across cities and countries by counting unique orders.
- Used SQL window functions to rank products by quantity sold in each region to identify top-selling products.

**Revenue Impact Assessment**

- Summarized total and average transaction revenue by city and country to assess regional performance.
- Combined sales and transaction data through additional queries to provide a holistic view of revenue distribution.

### Step 5: Quality Assurance (QA) of My Data

 **Identifying Missing Records**:
  - Checked for sessions in `analytics` that don't exist in `All_sessions`.
  - Checked for sessions in `All_sessions` that don't exist in `analytics`.

 **Counting Total Sessions**:
  - Compared total session counts between `All_sessions` and `analytics` to check for discrepancies.

 **Analyzing for Duplicates**:
  - Identified and removed duplicates in both `All_sessions` and `analytics` using `GROUP BY` and `COUNT()`.

 **Product Data Validation**:
  - Checked for missing or mismatched `productsku` values across `All_sessions`, `sales_report`, and `sales_by_sku`.

 **Checking Aggregated Data**:
  - Compared total ordered products (`total_ordered`) between `sales_by_sku` and `sales_report` to identify mismatches.


## Results

- The United States generated the highest transaction revenues, with cities like San Francisco, Sunnyvale, and Palo Alto standing out as key markets.
- Madrid, Spain, had the highest average number of products ordered per visitor, indicating strong consumer engagement.
- The product category `"Home/Nest/Nest-USA/"` was the most frequently purchased, with demand peaking in the United States, especially in cities like Mountain View and New York.
- The `Waze Dress Socks` was the best-selling product in Spain (Madrid) with 10 total orders. In the U.S., cities had various best-selling products, such as the `"Red Spiral Google Notebook"`.
- Bounce rates were highest in U.S. cities like Mountain View and New York, which could indicate potential UX issues or marketing misalignment.
- Organic search was the dominant traffic source, followed by Direct, reinforcing the need for effective SEO strategies.

## Challenges

- One of the biggest hurdles was dealing with redundant tables and missing values. This made the cleaning process more complex than expected. Many columns contained overlapping information, such as product quantity and total ordered, but their values were often inconsistent. This required careful judgment to decide which data to trust. In some cases, I eliminated certain tables entirely to streamline the analysis.
- Duplicate and null values were another significant challenge. Despite the large dataset, many queries returned minimal results because so much data was incomplete or unreliable. For instance, the United States had enough data to generate meaningful insights, but many other countries had very little usable data.
- Finding the best approach to clean the data took multiple iterations. Initially, I used view tables to organize and refine the dataset, but I had to make several adjustments along the way to ensure the data was accurate and consistent.

## Future Goals

If I had more time, I would have expanded my analysis by diving deeper into the date and time data. Analyzing sales trends over different periods could have provided valuable insights into seasonal variations, peak shopping periods, and the impact of different times of day or week on customer behavior. This could help improve sales forecasting and optimize marketing campaigns to align with these trends.

I would also have explored other tables in the dataset, such as the `sales_report` table. By comparing queries from this table with those from `all_sessions`, I could have assessed how the results aligned and whether one table provided more meaningful or reliable insights. Since both tables had similar columns, comparing them would have helped determine which was more useful for the analysis and potentially improved the quality of the findings.
