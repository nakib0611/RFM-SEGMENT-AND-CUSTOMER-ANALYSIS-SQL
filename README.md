# 📊 RFM Segmentation & Sales Analysis Project

## 📌 Project Overview
This project uses SQL to analyze sales data for an automobile retailer. The main goal was to segment customers using the **RFM (Recency, Frequency, Monetary)** technique to identify VIPs, loyal customers, and those at risk. Additionally, I performed exploratory analysis to uncover sales trends and product performance.

---

## 1. Data Overview
Before analyzing, I inspected the raw dataset to understand the available columns and data types.

```sql
SELECT * FROM SAMPLE_RFM LIMIT 5;
```
-- OUTPUT --
### 1. Data Overview
Below is a sample of the raw data (showing the first 5 rows):


| ORDERNUMBER | QUANTITYORDERED | PRICEEACH | ORDERLINENUMBER | SALES   | ORDERDATE | STATUS  | QTR_ID | MONTH_ID | YEAR_ID | PRODUCTLINE | MSRP | PRODUCTCODE | CUSTOMERNAME          | PHONE       | ADDRESSLINE1            | ADDRESSLINE2 | CITY          | STATE | POSTALCODE | COUNTRY | TERRITORY | CONTACTLASTNAME | CONTACTFIRSTNAME | DEALSIZE |
|-------------|-----------------|-----------|-----------------|---------|-----------|---------|--------|----------|---------|-------------|------|-------------|-----------------------|-------------|-------------------------|--------------|---------------|-------|------------|---------|-----------|-----------------|------------------|----------|
| 10107       | 30.00           | 95.70     | 2               | 2871.00 | 24/2/03   | Shipped | 1      | 2        | 2003    | Motorcycles | 95   | S10_1678    | Land of Toys Inc.     | 2125557818  | 897 Long Airport Avenue |              | NYC           | NY    | 10022      | USA     | NA        | Yu              | Kwai             | Small    |
| 10121       | 34.00           | 81.35     | 5               | 2765.90 | 7/5/03    | Shipped | 2      | 5        | 2003    | Motorcycles | 95   | S10_1678    | Reims Collectables    | 26.47.1555  | 59 rue de l'Abbaye      |              | Reims         |       | 51100      | France  | EMEA      | Henriot         | Paul             | Small    |
| 10134       | 41.00           | 94.74     | 2               | 3884.34 | 1/7/03    | Shipped | 3      | 7        | 2003    | Motorcycles | 95   | S10_1678    | Lyon Souveniers       | +33 1 46 62 | 27 rue du Colonel Pierre|              | Paris         |       | 75508      | France  | EMEA      | Da Cunha        | Daniel           | Medium   |
| 10145       | 45.00           | 83.26     | 6               | 3746.70 | 25/8/03   | Shipped | 3      | 8        | 2003    | Motorcycles | 95   | S10_1678    | Toys4GrownUps.com     | 6265557265  | 78934 Hillside Dr.      |              | Pasadena      | CA    | 90003      | USA     | NA        | Young           | Julie            | Medium   |
| 10159       | 49.00           | 100.00    | 14              | 5205.27 | 10/10/03  | Shipped | 4      | 10       | 2003    | Motorcycles | 95   | S10_1678    | Corporate Gift Ideas  | 6505551386  | 7734 Strong St.         |              | San Francisco | CA    |            | USA     | NA        | Brown           | Julie            | Medium   |
