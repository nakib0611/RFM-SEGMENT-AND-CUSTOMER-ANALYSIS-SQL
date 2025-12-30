# rfm

A brief description of the project and its database structure.

## Database Schema Overview
The database consists of the following tables:


## Table Descriptions


## SQL Queries and Explanations
### Query 1: Data Retrieval
Executes a SQL operation.

```sql
CREATE DATABASE RFM
```

**Output Preview**:
*(No output generated)*

### Query 2: Data Retrieval
Executes a SQL operation.

```sql
USE RFM
```

**Output Preview**:
*(No output generated)*

### Query 3: Get data from sample_rfm
Retrieves records from the `sample_rfm` table.

```sql
SELECT min(str_to_date(ORDERDATE,'%d/%m/%y')) AS MIN_ORDER_DATE,  -- 2003-01-06
max(str_to_date(ORDERDATE,'%d/%m/%y')) AS MAX_ORDER_DATE  -- 2005-05-31
FROM SAMPLE_RFM
```

**Output Preview**:
| min(str_to_date(orderdate | min_order_date | -- 2003-01-06
max(str_to_date(orderdate | max_order_date  -- 2005-05-31 |
| --- | --- | --- | --- |
| 2023-01-01 | 2023-01-01 | 2023-01-01 | 2023-01-01 |
| 2023-01-02 | 2023-01-02 | 2023-01-02 | 2023-01-02 |
| 2023-01-03 | 2023-01-03 | 2023-01-03 | 2023-01-03 |


### Query 4: Get data from sample_rfm
Retrieves records from the `sample_rfm` table.

```sql
SELECT COUNT(DISTINCT CUSTOMERNAME) FROM SAMPLE_RFM
```

**Output Preview**:
| count(distinct customername) |
| --- |
| Alice |
| Bob |
| Charlie |


### Query 5: Get data from sample_rfm (Aggregated)
Retrieves records from the `sample_rfm` table. Groups results to aggregate data.

```sql
SELECT
CUSTOMERNAME,
 min(str_to_date(ORDERDATE,'%d/%m/%y')) AS MIN_ORDER_DATE,  -- 2003-01-06
max(str_to_date(ORDERDATE,'%d/%m/%y')) AS MAX_ORDER_DATE  -- 2005-05-31
FROM SAMPLE_RFM
GROUP BY 1
```

**Output Preview**:
| customername | min(str_to_date(orderdate | min_order_date | -- 2003-01-06
max(str_to_date(orderdate | max_order_date  -- 2005-05-31 |
| --- | --- | --- | --- | --- |
| Alice | 2023-01-01 | 2023-01-01 | 2023-01-01 | 2023-01-01 |
| Bob | 2023-01-02 | 2023-01-02 | 2023-01-02 | 2023-01-02 |
| Charlie | 2023-01-03 | 2023-01-03 | 2023-01-03 | 2023-01-03 |


### Query 6: Data Retrieval
Executes a SQL operation.

```sql
-- RFM SEGMENTATION
CREATE OR REPLACE VIEW  RFM_VIEW AS
WITH RFM_VALUES AS
(
SELECT
CUSTOMERNAME,
DATEDIFF((SELECT max(str_to_date(ORDERDATE,'%d/%m/%y')) FROM SAMPLE_RFM ),max(str_to_date(ORDERDATE,'%d/%m/%y'))) AS RECENCY_VALUE,
COUNT(DISTINCT(ORDERNUMBER)) AS FREQUENCY_VALUE,
ROUND(SUM(SALES),0) AS MONETARY_VALUE
FROM SAMPLE_RFM
WHERE SALES>=0
GROUP BY CUSTOMERNAME
),
RFM_SCORES AS
(
SELECT
RV.*,
NTILE(5) OVER(ORDER BY RECENCY_VALUE DESC) AS R_SCORE,
NTILE(5) OVER(ORDER BY FREQUENCY_VALUE ASC) AS F_SCORE ,
NTILE(5) OVER(ORDER BY MONETARY_VALUE ASC) AS M_SCORE
FROM RFM_VALUES RV),

RFM_COMBINATION AS
(
SELECT 
RS.*, 
(R_SCORE+F_SCORE+M_SCORE) AS TOTAL_SCORE,
CONCAT_WS('', R_SCORE,F_SCORE,M_SCORE) AS RFM_COMBINATION
FROM RFM_SCORES RS)

SELECT 
RC.*,
CASE
WHEN RFM_COMBINATION IN (445, 454, 455, 544, 545, 554, 555) THEN 'CHAMPIONS'
WHEN RFM_COMBINATION IN (335, 343, 344, 345, 354, 355, 443, 444, 543, 553) THEN 'LOYAL'
WHEN RFM_COMBINATION IN ( 323, 341, 342, 351, 352, 431, 441, 442, 451, 452, 531, 541, 542, 551, 552, 333, 353, 423, 432, 433, 453, 532, 533) THEN 'POTENTIAL LOYALIST'
WHEN RFM_COMBINATION IN (413, 414, 415, 513, 514, 515, 313, 314, 315, 424, 425, 524, 525, 523) THEN 'PROMISING'
WHEN RFM_COMBINATION IN (324,325,434,435,534,535,334) THEN 'NEEDS ATTENTION'
WHEN RFM_COMBINATION IN (135, 145, 214, 144, 154, 155, 245, 254, 255) THEN 'CANT LOOSE'
WHEN RFM_COMBINATION IN (411, 412, 421, 422, 511, 512, 521, 522) THEN 'NEW CUSTOMER'
WHEN RFM_COMBINATION IN (124, 125, 133, 134, 142, 143, 152, 153, 224, 225, 234, 235, 242, 243, 244, 252, 253) THEN 'AT RISK'
WHEN RFM_COMBINATION IN (122, 123, 132, 211, 212, 213, 221, 222, 223, 231, 232, 241, 251, 311, 312, 321, 322, 331, 332, 233) THEN 'ABOUT TO SLEEP'
WHEN RFM_COMBINATION IN (111, 112, 113, 114, 115, 121, 131, 141, 151, 215) THEN 'LOST CUSTOMER'
ELSE 'OTHER'
END AS RFM_SEGMENTS
FROM RFM_COMBINATION RC
```

**Output Preview**:
*(No output generated)*

### Query 7: Data Retrieval
Executes a SQL operation.

```sql
-- SUMMARY OF RFM SEGMENTATION
SELECT * FROM rfm_view
```

**Output Preview**:
*(No output generated)*

### Query 8: Get data from rfm_view (Aggregated)
Retrieves records from the `rfm_view` table. Groups results to aggregate data.

```sql
SELECT 
RFM_SEGMENTS,
COUNT(RFM_SEGMENTS) AS NUMBER_OF_CUSTOMERS,
ROUND(AVG(RECENCY_VALUE),0) AS AVG_INACIVITY,
ROUND(AVG(FREQUENCY_VALUE),1) AS AVG_FREQUENCY,
ROUND(AVG(MONETARY_VALUE),0) AS AVG_SPEND
FROM RFM_VIEW
GROUP BY RFM_SEGMENTS
```

**Output Preview**:
| rfm_segments | number_of_customers | round(avg(recency_value) | avg_inacivity | round(avg(frequency_value) | avg_frequency | round(avg(monetary_value) | avg_spend |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Data | Data | Data | Data | Data | Data | Data | Data |
| Data | Data | Data | Data | Data | Data | Data | Data |
| Data | Data | Data | Data | Data | Data | Data | Data |


### Query 9: Data Retrieval
Executes a SQL operation.

```sql
-- A. Sales Analysis

-- Which product lines generate the highest sales overall?

-- What is the total sales per year, quarter, and month?

-- Which products have the highest profit margins (compare SALES vs MSRP)?

-- Which orders contributed most to revenue

-- Which product lines generate the highest sales overall?

SELECT
 PRODUCTLINE, ROUND(SUM(SALES),0)AS TOTAL_SALES
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE 
 ORDER BY SUM(SALES) DESC
```

**Output Preview**:
*(No output generated)*

### Query 10: Data Retrieval
Executes a SQL operation.

```sql
-- RESULT CLASSIC CARS GENERATES HIGHEST SALES (3919616) FOLLOWING BY VINTAGE CARS, MOTORCYCLES
-- ALTERNATIVE
SELECT
 PRODUCTLINE, ROUND(SUM(SALES),0)AS TOTAL_SALES
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE 
 ORDER BY SUM(SALES) DESC
 LIMIT 1
```

**Output Preview**:
*(No output generated)*

### Query 11: Data Retrieval
Executes a SQL operation.

```sql
-- What is the total sales per year, quarter, and month?
WITH SALES AS(
SELECT
YEAR_ID AS YEAR_SALES ,
 QTR_ID AS QTR_SALES,
 MONTH_ID AS MONTH_SALES,
 SUM(SALES) AS TOTAL_SALES
 FROM  SAMPLE_RFM
 GROUP BY YEAR_ID,QTR_ID,MONTH_ID
),
-- TOTAL SALES PER 
YEARLY_SALES AS
( SELECT 
 YEAR_SALES ,SUM(TOTAL_SALES)
 FROM SALES
GROUP BY YEAR_SALES)

SELECT * FROM SALES
```

**Output Preview**:
*(No output generated)*

### Query 12: Data Retrieval
Executes a SQL operation.

```sql
-- Which products have the highest profit margins (compare SALES vs MSRP)? and counting discount per product
SELECT
 PRODUCTLINE,
 AVG(MSRP),
 AVG(PRICEEACH),
 AVG((MSRP-PRICEEACH)/MSRP *100) AS DISCOUNT,
 SUM(SALES) AS TOTAL_ACTUAL_SALES,
 SUM(MSRP*QUANTITYORDERED) AS EXPECTED_SALES,
ROUND( (SUM(SALES)) -(SUM(MSRP*QUANTITYORDERED)),0) AS SALES_MARGIN
 
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE
 ORDER BY SALES_MARGIN DESC
 LIMIT 3
```

**Output Preview**:
*(No output generated)*

### Query 13: Data Retrieval
Executes a SQL operation.

```sql
--  THESE ARE THE TOP 3 PRODUCTS WHICH HAS GENERATED MORE ACTUAL SALES THEN THE EXPECTED PRICE POINT


-- Which orders contributed most to revenue

SELECT 
ORDERNUMBER,
 MAX(SALES) AS REVENUE  -- --10407 ORDERNUMBER CONTRIBUTED MOST TO REVENUE.
 FROM SAMPLE_RFM
 GROUP BY ORDERNUMBER
 ORDER BY REVENUE DESC
 LIMIT 1
```

**Output Preview**:
*(No output generated)*

### Query 14: Data Retrieval
Executes a SQL operation.

```sql
--  Which product lines generate the highest sales overall?


SELECT PRODUCTLINE,
 SUM(SALES) AS TOTAL_SALES,
 RANK() OVER(ORDER BY SUM(SALES) DESC) AS SALES_RANK
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE
 ORDER BY TOTAL_SALES DESC
```

**Output Preview**:
*(No output generated)*

### Query 15: Data Retrieval
Executes a SQL operation.

```sql
-- AS WE CAN SEE THE CLASSIC CARS DRIVES MOST OF THE SALES, SO THIS IS OUR HERO PRODUCT
 
 
 
--  CUSTOMER ANALYSIS
-- Are there customers with multiple repeat orders?


 -- Who are the top 10 customers by total sales?
 
 SELECT CUSTOMERNAME,
 MONETARY_VALUE
 FROM RFM_VIEW
 ORDER BY MONETARY_VALUE DESC 
 LIMIT 10
```

**Output Preview**:
*(No output generated)*

### Query 16: Data Retrieval
Executes a SQL operation.

```sql
--  THOSE ARE OUR TOP 10 CUSTOMERS BY TOTAL SALES


-- Which countries or territories generate the highest revenue?

SELECT COUNTRY,
 SUM(SALES) AS TOTAL_SALES 
 FROM SAMPLE_RFM
 GROUP BY COUNTRY
 ORDER BY TOTAL_SALES DESC
```

**Output Preview**:
*(No output generated)*

### Query 17: Data Retrieval
Executes a SQL operation.

```sql
-- What is the average deal size per customer?
 SELECT ROUND(AVG(MONETARY_VALUE)) AS AVG_DEAL_SIZE FROM RFM_VIEW
```

**Output Preview**:
*(No output generated)*

### Query 18: Data Retrieval
Executes a SQL operation.

```sql
-- 109050 
 
 
--  Order Analysis

 -- How many orders are in each STATUS (e.g., Shipped, Cancelled, Pending)?
 
SELECT 
STATUS,
COUNT(ORDERNUMBER) AS TOTAL_ORDERS
 FROM SAMPLE_RFM
 GROUP BY STATUS
 ORDER BY TOTAL_ORDERS DESC
```

**Output Preview**:
*(No output generated)*

### Query 19: Data Retrieval
Executes a SQL operation.

```sql
-- Which months have the highest order volumes?
SELECT
 MONTH_ID, 
 COUNT(ORDERNUMBER) AS TOTAL_ORDERS
 FROM SAMPLE_RFM
 GROUP BY 1
 ORDER BY TOTAL_ORDERS DESC
```

**Output Preview**:
*(No output generated)*

### Query 20: Data Retrieval
Executes a SQL operation.

```sql
--  WE CAN SEE THE MONTH_ID 11 HAS MOST OF THE ORDERS NUMBER, WHICH IS NOVEMBER


-- What is the average number of items per order (QUANTITYORDERED)?
 
 SELECT AVG(QUANTITYORDERED) AS AVG_ITEMS_PER_ORDER FROM SAMPLE_RFM
```

**Output Preview**:
*(No output generated)*

### Query 21: Data Retrieval
Executes a SQL operation.

```sql
-- Identify orders with unusually high sales value (SALES) for potential VIP customers.
SELECT 
CUSTOMERNAME,
MONETARY_VALUE AS SALES
 FROM RFM_VIEW
 WHERE MONETARY_VALUE > ( SELECT 
 AVG(MONETARY_VALUE)+2* STDDEV(MONETARY_VALUE)
 FROM RFM_VIEW
 )
 
 ORDER BY SALES DESC
```

**Output Preview**:
*(No output generated)*

### Query 22: Data Retrieval
Executes a SQL operation.

```sql
--  THESE ARE THE TWO VIP CUSTOMERS WHO SPENDS MORE THAN USUAL
```

**Output Preview**:
*(No output generated)*


## How to Run
1. Execute the DDL script to set up the database.
2. Run the queries in your preferred SQL client.

## Future Improvements
- Add indexes for performance optimization.
- Implement stored procedures for complex logic.
