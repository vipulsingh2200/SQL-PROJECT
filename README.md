# SQL-PROJECT
##FINANCIAL DATABASE 


create database financialDatabase;

use financialDatabase;

-- Create a table for financial model
CREATE TABLE FinancialData (
  CompanyID INT,
  Year INT,
  Revenue DECIMAL(12, 2),
  Expenses DECIMAL(12, 2),
  Profit DECIMAL(12, 2),
  EarningsPerShare DECIMAL(8, 2)
);

-- Insert 20 records into the table
INSERT INTO FinancialData (CompanyID, Year, Revenue, Expenses, Profit, EarningsPerShare)
VALUES
  (1, 2010, 1000000.00, 800000.00, 200000.00, 2.50),
  (1, 2011, 1100000.00, 900000.00, 200000.00, 2.00),
  (1, 2012, 1200000.00, 950000.00, 250000.00, 2.75),
  (2, 2010, 500000.00, 400000.00, 100000.00, 1.00),
  (2, 2011, 550000.00, 450000.00, 100000.00, 1.50),
  (2, 2012, 600000.00, 480000.00, 120000.00, 1.75),
  (3, 2010, 800000.00, 700000.00, 100000.00, 0.75),
  (3, 2011, 850000.00, 750000.00, 100000.00, 0.90),
  (3, 2012, 900000.00, 800000.00, 100000.00, 1.00),
  (4, 2010, 1500000.00, 1200000.00, 300000.00, 3.00),
  (4, 2011, 1600000.00, 1300000.00, 300000.00, 2.75),
  (4, 2012, 1700000.00, 1400000.00, 300000.00, 2.50),
  (5, 2010, 2000000.00, 1800000.00, 200000.00, 1.50),
  (5, 2011, 2200000.00, 2000000.00, 200000.00, 1.25),
  (5, 2012, 2400000.00, 2200000.00, 200000.00, 1.00),
  (6, 2010, 3000000.00, 2800000.00, 200000.00, 0.75),
  (6, 2011, 3300000.00, 3100000.00, 200000.00, 0.50),
  (6, 2012, 3600000.00, 3400000.00, 200000.00, 0.25),
  (7, 2010, 100000.00, 80000.00, 20000.00, 0.20),
  (7, 2011, 110000.00, 90000.00, 20000.00, 0.25),
  (7, 2012, 120000.00, 95000.00, 25000.00, 0.30);



-- Logical questions

-- 1. What is the total revenue for each year?
SELECT YEAR,SUM(Revenue) 
from FinancialData
GROUP BY YEAR;

-- 2. What is the average profit for each company?
SELECT CompanyID,AVG(Profit) AS avg_profit
from FinancialData
GROUP BY CompanyID;

-- 3. Which year had the highest expenses?
SELECT YEAR,SUM(Expenses) AS highest_expenses
from FinancialData
GROUP BY YEAR
ORDER BY highest_expenses DESC
LIMIT 1;
-- 4. What is the total profit for CompanyID 4?
SELECT CompanyID,SUM(profit) AS total_profit
from FinancialData
WHERE CompanyID = 4;

-- 5. What is the average earnings per share for each year?
SELECT YEAR, AVG(EarningsPerShare) AS AVG_EPY
from financialdata
GROUP BY YEAR;

-- 6. Which company had the highest revenue in 2012?
SELECT CompanyID, Revenue 
from financialdata
WHERE YEAR = 2012
ORDER BY Revenue DESC
limit 1;

-- 7. What is the total profit for all companies in 2011?
SELECT CompanyID, SUM(Profit) AS total_profit
from financialdata
WHERE YEAR = 2011
GROUP BY CompanyID;


-- 8. What is the maximum earnings per share for each company?
SELECT CompanyID, max(EarningsPerShare) AS max_epy
from financialdata
GROUP BY CompanyID;
-- 9. Which company had the highest profit in 2010?
SELECT CompanyID, Profit
from financialdata
where year = 2010
ORDER BY Profit desc;
-- 10. What is the average revenue for the years 2010-2012?
SELECT AVG(Revenue) AS avg_revenue
from financialdata
where YEAR BETWEEN 2010 AND 2012;


-- 11. Which company had the highest average annual growth rate in revenue from 2010 to 2012?
SELECT CompanyID, 
			(max(Revenue) - min(Revenue))/min(Revenue) * 100 AS ANGR 
            FROM financialdata
            WHERE YEAR BETWEEN 2010 AND 2012
            GROUP BY CompanyID
            ORDER BY ANGR DESC
            LIMIT 1;





-- 12. What is the percentage change in profit for each company from 2010 to 2012?
SELECT f1.CompanyID,
       ((f2.profit-f1.profit)/f1.profit) * 100 AS profitchange
       FROM financialdata AS f1
       JOIN financialdata AS f2 ON f1.CompanyID = f2.CompanyID
       WHERE f1.YEAR = 2010 AND f2.Year = 2012;


-- 13. Which companies had a consistent increase in profit every year from 2010 to 2012?
SELECT CompanyID 
FROM FinancialData 
GROUP BY CompanyID 
HAVING MIN(Profit) < ANY (SELECT Profit 
                          FROM FinancialData 
                          WHERE YEAR = 2010 AND FinancialData.CompanyID= FinancialData.CompanyID)
   AND MIN(Profit) < ANY (SELECT Profit 
                          FROM FinancialData 
                          WHERE YEAR = 2012 AND FinancialData.CompanyID= FinancialData.CompanyID);
           
           
		      
-- 14. What is the year-over-year growth rate in earnings per share for CompanyID 3?
SELECT Year, 
       (EarningsPerShare - LAG(EarningsPerShare) OVER (PARTITION BY CompanyID ORDER BY Year)) / LAG(EarningsPerShare) OVER (PARTITION BY CompanyID ORDER BY Year) * 100 AS EPSGrowthRate
FROM FinancialData
WHERE CompanyID = 3;
