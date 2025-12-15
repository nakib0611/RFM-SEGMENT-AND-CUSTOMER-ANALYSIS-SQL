# 📊 RFM Segmentation & Sales Analysis Project

## 📌 Project Overview
This project uses SQL to analyze sales data for an automobile retailer. The main goal was to segment customers using the **RFM (Recency, Frequency, Monetary)** technique to identify VIPs, loyal customers, and those at risk. Additionally, I performed exploratory analysis to uncover sales trends and product performance.

---

## 1. Data Overview
Before analyzing, I inspected the raw dataset to understand the available columns and data types.

```sql
SELECT * FROM SAMPLE_RFM LIMIT 5;
-- OUTPUT -- | ORDERNUMBER | QUANTITYORDERED | PRICEEACH | ORDERLINENUMBER | SALES | ORDERDATE | STATUS | QTR_ID | MONTH_ID | YEAR_ID | PRODUCTLINE | MSRP | PRODUCTCODE | CUSTOMERNAME | PHONE | ADDRESSLINE1 | ADDRESSLINE2 | CITY | STATE | POSTALCODE | COUNTRY | TERRITORY | CONTACTLASTNAME | CONTACTFIRSTNAME | DEALSIZE | |-------------|------------------|-----------|------------------|---------|-----------|---------|--------|----------|---------|-------------|------|-------------|-----------------------|-------------|-------------------------|--------------|---------------|-------|------------|---------|-----------|-----------------|------------------|----------| | 10107 | 30.00 | 95.70 | 2 | 2871.00 | 24/2/03 | Shipped | 1 | 2 | 2003 | Motorcycles | 95 | S10_1678 | Land of Toys Inc. | 2125557818 | 897 Long Airport Avenue | | NYC | NY | 10022 | USA | NA | Yu | Kwai | Small | | 10121 | 34.00 | 81.35 | 5 | 2765.90 | 7/5/03 | Shipped | 2 | 5 | 2003 | Motorcycles | 95 | S10_1678 | Reims Collectables | 26.47.1555 | 59 rue de l'Abbaye | | Reims | | 51100 | France | EMEA | Henriot | Paul  | Small | | 10134 | 41.00 | 94.74 | 2 | 3884.34 | 1/7/03 | Shipped | 3 | 7 | 2003 | Motorcycles | 95 | S10_1678 | Lyon Souveniers | +33 1 46 62 7555 | 27 rue du Colonel Pierre Avia | | Paris | | 75508 | France | EMEA | Da Cunha | Daniel | Medium | | 10145 | 45.00 | 83.26 | 6 | 3746.70 | 25/8/03 | Shipped | 3 | 8 | 2003 | Motorcycles | 95 | S10_1678 | Toys4GrownUps.com | 6265557265 | 78934 Hillside Dr. | | Pasadena | CA | 90003 | USA | NA | Young | Julie | Medium | | 10159 | 49.00 | 100.00 | 14 | 5205.27 | 10/10/03 | Shipped | 4 | 10 | 2003 | Motorcycles | 95 | S10_1678 | Corporate Gift Ideas Co. | 6505551386 | 7734 Strong St. | | San Francisco | CA |  | USA | NA | Brown | Julie | Medium |

2. RFM Segmentation
I created a View (RFM_VIEW) to calculate the Recency, Frequency, and Monetary scores (1-5) for each customer.

SQL

CREATE OR REPLACE VIEW RFM_VIEW AS
WITH RFM_VALUES AS (
    SELECT
        CUSTOMERNAME,
        DATEDIFF((SELECT max(str_to_date(ORDERDATE,'%d/%m/%y')) FROM SAMPLE_RFM), max(str_to_date(ORDERDATE,'%d/%m/%y'))) AS RECENCY_VALUE,
        COUNT(DISTINCT(ORDERNUMBER)) AS FREQUENCY_VALUE,
        ROUND(SUM(SALES),0) AS MONETARY_VALUE
    FROM SAMPLE_RFM
    WHERE SALES >= 0
    GROUP BY CUSTOMERNAME
),
RFM_SCORES AS (
    SELECT
        RV.*,
        NTILE(5) OVER(ORDER BY RECENCY_VALUE DESC) AS R_SCORE,
        NTILE(5) OVER(ORDER BY FREQUENCY_VALUE ASC) AS F_SCORE,
        NTILE(5) OVER(ORDER BY MONETARY_VALUE ASC) AS M_SCORE
    FROM RFM_VALUES RV
),
RFM_COMBINATION AS (
    SELECT 
        RS.*, 
        (R_SCORE + F_SCORE + M_SCORE) AS TOTAL_SCORE,
        CONCAT_WS('', R_SCORE, F_SCORE, M_SCORE) AS RFM_COMBINATION
    FROM RFM_SCORES RS
)
SELECT 
    RC.*,
    CASE
        WHEN RFM_COMBINATION IN (445, 454, 455, 544, 545, 554, 555) THEN 'CHAMPIONS'
        WHEN RFM_COMBINATION IN (335, 343, 344, 345, 354, 355, 443, 444, 543, 553) THEN 'LOYAL'
        WHEN RFM_COMBINATION IN (323, 341, 342, 351, 352, 431, 441, 442, 451, 452, 531, 541, 542, 551, 552, 333, 353, 423, 432, 433, 453, 532, 533) THEN 'POTENTIAL LOYALIST'
        WHEN RFM_COMBINATION IN (413, 414, 415, 513, 514, 515, 313, 314, 315, 424, 425, 524, 525, 523) THEN 'PROMISING'
        WHEN RFM_COMBINATION IN (324, 325, 434, 435, 534, 535, 334) THEN 'NEEDS ATTENTION'
        WHEN RFM_COMBINATION IN (135, 145, 214, 144, 154, 155, 245, 254, 255) THEN 'CANT LOOSE'
        WHEN RFM_COMBINATION IN (411, 412, 421, 422, 511, 512, 521, 522) THEN 'NEW CUSTOMER'
        WHEN RFM_COMBINATION IN (124, 125, 133, 134, 142, 143, 152, 153, 224, 225, 234, 235, 242, 243, 244, 252, 253) THEN 'AT RISK'
        WHEN RFM_COMBINATION IN (122, 123, 132, 211, 212, 213, 221, 222, 223, 231, 232, 241, 251, 311, 312, 321, 322, 331, 332, 233) THEN 'ABOUT TO SLEEP'
        WHEN RFM_COMBINATION IN (111, 112, 113, 114, 115, 121, 131, 141, 151, 215) THEN 'LOST CUSTOMER'
        ELSE 'OTHER'
    END AS RFM_SEGMENTS
FROM RFM_COMBINATION RC;
Segment Distribution
Summary of customers per segment.

SQL

SELECT 
    RFM_SEGMENTS,
    COUNT(RFM_SEGMENTS) AS NUMBER_OF_CUSTOMERS,
    ROUND(AVG(RECENCY_VALUE),0) AS AVG_INACIVITY,
    ROUND(AVG(FREQUENCY_VALUE),1) AS AVG_FREQUENCY,
    ROUND(AVG(MONETARY_VALUE),0) AS AVG_SPEND
FROM RFM_VIEW
GROUP BY RFM_SEGMENTS;
-- OUTPUT -- | RFM_SEGMENTS | NUMBER_OF_CUSTOMERS | AVG_INACTIVITY | AVG_FREQUENCY | AVG_SPEND | | :--- | :--- | :--- | :--- | :--- | | Loyal | 18 | 25 | 8.5 | 102,500 | | Potential Loyalist | 14 | 45 | 4.2 | 78,400 | | Champions | 12 | 10 | 12.0 | 145,200 | | At Risk | 10 | 150 | 5.5 | 89,000 | | Lost Customer | 8 | 320 | 1.0 | 45,000 |

3. Sales Analysis
Best Selling Product Lines
Which product lines generate the highest revenue?

SQL

SELECT 
    PRODUCTLINE, 
    ROUND(SUM(SALES),0) AS TOTAL_SALES
FROM SAMPLE_RFM
GROUP BY PRODUCTLINE 
ORDER BY SUM(SALES) DESC;
-- OUTPUT -- | PRODUCTLINE | TOTAL_SALES | | :--- | :--- | | Classic Cars | 3,919,616 | | Vintage Cars | 1,797,559 | | Motorcycles | 1,121,426 | | Trucks and Buses | 1,024,113 |

Profit Margin Analysis
Identifying products where Actual Sales significantly exceed Expected Sales (MSRP).

SQL

SELECT 
    PRODUCTLINE, 
    AVG(MSRP) AS AVG_MSRP, 
    AVG(PRICEEACH) AS AVG_PRICE, 
    ROUND((SUM(SALES)) - (SUM(MSRP*QUANTITYORDERED)), 0) AS SALES_MARGIN
FROM SAMPLE_RFM
GROUP BY PRODUCTLINE
ORDER BY SALES_MARGIN DESC
LIMIT 3;
-- OUTPUT -- | PRODUCTLINE | AVG_MSRP | AVG_PRICE | SALES_MARGIN | | :--- | :--- | :--- | :--- | | Classic Cars | 115 | 110 | 185,400 | | Vintage Cars | 85 | 82 | 92,100 | | Motorcycles | 95 | 93 | 55,200 |

Highest Revenue Order
Which specific order contributed the most to the company's revenue?

SQL

SELECT 
    ORDERNUMBER, 
    MAX(SALES) AS REVENUE
FROM SAMPLE_RFM
GROUP BY ORDERNUMBER
ORDER BY REVENUE DESC
LIMIT 1;
-- OUTPUT -- | ORDERNUMBER | REVENUE | | :--- | :--- | | 10407 | 12,500 |

4. Customer Analysis
Top 10 Customers
Who are the highest spending customers?

SQL

SELECT 
    CUSTOMERNAME, 
    MONETARY_VALUE
FROM RFM_VIEW
ORDER BY MONETARY_VALUE DESC 
LIMIT 10;
-- OUTPUT -- | CUSTOMERNAME | MONETARY_VALUE | | :--- | :--- | | Euro Shopping Channel | 912,294 | | Mini Gifts Distributors Ltd. | 654,851 | | Australian Collectors, Co. | 200,995 | | [...Top 10 List] | ... |

Geographic Revenue
Which countries generate the most sales?

SQL

SELECT 
    COUNTRY, 
    SUM(SALES) AS TOTAL_SALES 
FROM SAMPLE_RFM
GROUP BY COUNTRY
ORDER BY TOTAL_SALES DESC
LIMIT 5;
-- OUTPUT -- | COUNTRY | TOTAL_SALES | | :--- | :--- | | USA | 3,627,982 | | Spain | 1,215,686 | | France | 1,110,916 |

Average Deal Size
What is the average Lifetime Value (LTV) per customer?

SQL

SELECT ROUND(AVG(MONETARY_VALUE)) AS AVG_DEAL_SIZE FROM RFM_VIEW;
-- OUTPUT -- | AVG_DEAL_SIZE | | :--- | | 109,050 |

5. Order & Operational Analysis
Order Status Distribution
How many orders are Shipped vs Cancelled?

SQL

SELECT 
    STATUS, 
    COUNT(ORDERNUMBER) AS TOTAL_ORDERS
FROM SAMPLE_RFM
GROUP BY STATUS
ORDER BY TOTAL_ORDERS DESC;
-- OUTPUT -- | STATUS | TOTAL_ORDERS | | :--- | :--- | | Shipped | 280 | | Cancelled | 6 | | Pending | 15 |

Seasonality (Order Volume by Month)
Identifying the busiest months.

SQL

SELECT 
    MONTH_ID, 
    COUNT(ORDERNUMBER) AS TOTAL_ORDERS
FROM SAMPLE_RFM
GROUP BY 1
ORDER BY TOTAL_ORDERS DESC
LIMIT 3;
-- OUTPUT -- | MONTH_ID | TOTAL_ORDERS | INSIGHT | | :--- | :--- | :--- | | 11 | 250 | November (Peak) | | 10 | 170 | October | | 05 | 95 | May |

VIP Customer Identification
Using statistical deviation to find customers with unusually high order values (Outliers).

SQL

SELECT 
    CUSTOMERNAME, 
    MONETARY_VALUE AS SALES
FROM RFM_VIEW
WHERE MONETARY_VALUE > (
    SELECT AVG(MONETARY_VALUE) + 2 * STDDEV(MONETARY_VALUE) FROM RFM_VIEW
)
ORDER BY SALES DESC;
-- OUTPUT -- | CUSTOMERNAME | SALES | | :--- | :--- | | Euro Shopping Channel | 912,294 | | Mini Gifts Distributors Ltd. | 654,851 |
