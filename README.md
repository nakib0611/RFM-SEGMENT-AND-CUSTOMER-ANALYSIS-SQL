# Project Title

A brief description of the project and its database structure.

## Database Schema Overview
No tables defined.

## Table Descriptions
*No tables found or schema could not be parsed.*

## SQL Queries and Explanations
### Query 1: Create Structure
Defines a new database object.

```sql
CREATE DATABASE RFM
```

**Output Preview**:
| affected_rows | status |
| --- | --- |
| data | 2023-01-01 |
| data | 2023-01-02 |
| data | 2023-01-03 |


### Query 2: Data Retrieval
Executes a SQL operation.

```sql
USE RFM
```

**Output Preview**:
| affected_rows | status |
| --- | --- |
| data | 2023-01-01 |
| data | 2023-01-02 |
| data | 2023-01-03 |


### Query 3: Retrieve sample_rfm
Selects data from the **sample_rfm** table.

```sql
SELECT min(str_to_date(ORDERDATE,'%d/%m/%y')) AS MIN_ORDER_DATE,  
max(str_to_date(ORDERDATE,'%d/%m/%y')) AS MAX_ORDER_DATE  
FROM SAMPLE_RFM
```

**Output Preview**:
| min(str_to_date(orderdate | min_order_date | max(str_to_date(orderdate | max_order_date |
| --- | --- | --- | --- |
| 2023-01-01 | 2023-01-01 | 2023-01-01 | 2023-01-01 |
| 2023-01-02 | 2023-01-02 | 2023-01-02 | 2023-01-02 |
| 2023-01-03 | 2023-01-03 | 2023-01-03 | 2023-01-03 |


### Query 4: Retrieve sample_rfm
Selects data from the **sample_rfm** table.

```sql
SELECT COUNT(DISTINCT CUSTOMERNAME) FROM SAMPLE_RFM
```

**Output Preview**:
| count(distinct customername) |
| --- |
| Alice |
| Bob |
| Charlie |


### Query 5: Retrieve sample_rfm
Selects data from the **sample_rfm** table.

```sql
SELECT
CUSTOMERNAME,
 min(str_to_date(ORDERDATE,'%d/%m/%y')) AS MIN_ORDER_DATE,  
max(str_to_date(ORDERDATE,'%d/%m/%y')) AS MAX_ORDER_DATE  
FROM SAMPLE_RFM
GROUP BY 1
```

**Output Preview**:
| customername | min(str_to_date(orderdate | min_order_date | max(str_to_date(orderdate | max_order_date |
| --- | --- | --- | --- | --- |
| Alice | 2023-01-01 | 2023-01-01 | 2023-01-01 | 2023-01-01 |
| Bob | 2023-01-02 | 2023-01-02 | 2023-01-02 | 2023-01-02 |
| Charlie | 2023-01-03 | 2023-01-03 | 2023-01-03 | 2023-01-03 |


### Query 6: Create Structure
Defines a new database object.

```sql
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
| affected_rows | status |
| --- | --- |
| data | 2023-01-01 |
| data | 2023-01-02 |
| data | 2023-01-03 |


### Query 7: Retrieve rfm_view
Selects data from the **rfm_view** table.

```sql
SELECT * FROM rfm_view
```

**Output Preview**:
| id | name | created_at | status |
| --- | --- | --- | --- |
| 1 | Alice | 2023-01-01 | 2023-01-01 |
| 2 | Bob | 2023-01-02 | 2023-01-02 |
| 3 | Charlie | 2023-01-03 | 2023-01-03 |


### Query 8: Retrieve rfm_view
Selects data from the **rfm_view** table.

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
| data | data | data | data | data | data | data | data |
| data | data | data | data | data | data | data | data |
| data | data | data | data | data | data | data | data |


### Query 9: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT
 PRODUCTLINE, ROUND(SUM(SALES),0)AS TOTAL_SALES
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE 
 ORDER BY SUM(SALES) DESC
```

**Output Preview**:
| productline | round(sum(sales) | 0)as total_sales |
| --- | --- | --- |
| data | data | 10.99 |
| data | data | 25.5 |
| data | data | 99 |


### Query 10: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT
 PRODUCTLINE, ROUND(SUM(SALES),0)AS TOTAL_SALES
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE 
 ORDER BY SUM(SALES) DESC
 LIMIT 1
```

**Output Preview**:
| productline | round(sum(sales) | 0)as total_sales |
| --- | --- | --- |
| data | data | 10.99 |
| data | data | 25.5 |
| data | data | 99 |


### Query 11: Data Retrieval
Executes a SQL operation.

```sql
WITH SALES AS(
SELECT
YEAR_ID AS YEAR_SALES ,
 QTR_ID AS QTR_SALES,
 MONTH_ID AS MONTH_SALES,
 SUM(SALES) AS TOTAL_SALES
 FROM  SAMPLE_RFM
 GROUP BY YEAR_ID,QTR_ID,MONTH_ID
),

YEARLY_SALES AS
( SELECT 
 YEAR_SALES ,SUM(TOTAL_SALES)
 FROM SALES
GROUP BY YEAR_SALES)

SELECT * FROM SALES
```

**Output Preview**:
| affected_rows | status |
| --- | --- |
| data | 2023-01-01 |
| data | 2023-01-02 |
| data | 2023-01-03 |


### Query 12: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
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
| productline | avg(msrp) | avg(priceeach) | discount | total_actual_sales | expected_sales | round( (sum(sales)) -(sum(msrp*quantityordered)) | sales_margin |
| --- | --- | --- | --- | --- | --- | --- | --- |
| data | data | 10.99 | data | 10.99 | data | data | data |
| data | data | 25.5 | data | 25.5 | data | data | data |
| data | data | 99 | data | 99 | data | data | data |


### Query 13: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT 
ORDERNUMBER,
 MAX(SALES) AS REVENUE  
 FROM SAMPLE_RFM
 GROUP BY ORDERNUMBER
 ORDER BY REVENUE DESC
 LIMIT 1
```

**Output Preview**:
| ordernumber | revenue |
| --- | --- |
| data | data |
| data | data |
| data | data |


### Query 14: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT PRODUCTLINE,
 SUM(SALES) AS TOTAL_SALES,
 RANK() OVER(ORDER BY SUM(SALES) DESC) AS SALES_RANK
 FROM SAMPLE_RFM
 GROUP BY PRODUCTLINE
 ORDER BY TOTAL_SALES DESC
```

**Output Preview**:
| productline | total_sales | sales_rank |
| --- | --- | --- |
| data | 10.99 | data |
| data | 25.5 | data |
| data | 99 | data |


### Query 15: Retrieve rfm_view
Selects data from the **rfm_view** table. Sorts the output.

```sql
SELECT CUSTOMERNAME,
 MONETARY_VALUE
 FROM RFM_VIEW
 ORDER BY MONETARY_VALUE DESC 
 LIMIT 10
```

**Output Preview**:
| customername | monetary_value |
| --- | --- |
| Alice | data |
| Bob | data |
| Charlie | data |


### Query 16: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT COUNTRY,
 SUM(SALES) AS TOTAL_SALES 
 FROM SAMPLE_RFM
 GROUP BY COUNTRY
 ORDER BY TOTAL_SALES DESC
```

**Output Preview**:
| country | total_sales |
| --- | --- |
| data | 10.99 |
| data | 25.5 |
| data | 99 |


### Query 17: Retrieve rfm_view
Selects data from the **rfm_view** table.

```sql
SELECT ROUND(AVG(MONETARY_VALUE)) AS AVG_DEAL_SIZE FROM RFM_VIEW
```

**Output Preview**:
| avg_deal_size |
| --- |
| data |
| data |
| data |


### Query 18: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT 
STATUS,
COUNT(ORDERNUMBER) AS TOTAL_ORDERS
 FROM SAMPLE_RFM
 GROUP BY STATUS
 ORDER BY TOTAL_ORDERS DESC
```

**Output Preview**:
| status | total_orders |
| --- | --- |
| 2023-01-01 | 10.99 |
| 2023-01-02 | 25.5 |
| 2023-01-03 | 99 |


### Query 19: Retrieve sample_rfm
Selects data from the **sample_rfm** table. Aggregates data, Sorts the output.

```sql
SELECT
 MONTH_ID, 
 COUNT(ORDERNUMBER) AS TOTAL_ORDERS
 FROM SAMPLE_RFM
 GROUP BY 1
 ORDER BY TOTAL_ORDERS DESC
```

**Output Preview**:
| month_id | total_orders |
| --- | --- |
| 1 | 10.99 |
| 2 | 25.5 |
| 3 | 99 |


### Query 20: Retrieve sample_rfm
Selects data from the **sample_rfm** table.

```sql
SELECT AVG(QUANTITYORDERED) AS AVG_ITEMS_PER_ORDER FROM SAMPLE_RFM
```

**Output Preview**:
| avg_items_per_order |
| --- |
| data |
| data |
| data |


### Query 21: Retrieve rfm_view
Selects data from the **rfm_view** table. Filters specific rows, Sorts the output.

```sql
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
| customername | sales |
| --- | --- |
| Alice | data |
| Bob | data |
| Charlie | data |


## How to Run
1. Execute the DDL script to set up the database.
2. Run the queries in your preferred SQL client (e.g., MySQL Workbench, DBeaver).

## Future Improvements
- Add indexes for performance optimization.
- Implement stored procedures for complex logic.
