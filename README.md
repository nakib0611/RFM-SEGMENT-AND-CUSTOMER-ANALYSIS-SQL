# RFM-SEGMENT-AND-CUSTOMER-ANALYSIS-SQL
This project utilizes SQL to analyze and uncover meaningful customer behavior patterns.
'''CREATE DATABASE RFM;
USE RFM; SELECT min(str_to_date(ORDERDATE,'%d/%m/%y')) AS MIN_ORDER_DATE, -- 2003-01-06 max(str_to_date(ORDERDATE,'%d/%m/%y')) AS MAX_ORDER_DATE -- 2005-05-31 FROM SAMPLE_RFM;
SELECT COUNT(DISTINCT CUSTOMERNAME) FROM SAMPLE_RFM;
