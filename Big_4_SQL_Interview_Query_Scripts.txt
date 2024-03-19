DROP DATABASE IF EXISTS big_4;
CREATE DATABASE big_4;
USE big_4;

DROP TABLE IF EXISTS brands;
CREATE TABLE brands (Year INT, Brand VARCHAR (20), Amount INT);
INSERT INTO brands (Year, Brand, Amount) VALUES
(2018, 'Apple', 45000),
(2019, 'Apple', 35000),
(2020, 'Apple', 75000),
(2018, 'Samsung', 15000),
(2019, 'Samsung', 20000),
(2020, 'Samsung', 25000),
(2018, 'Nokia', 21000),
(2019, 'Nokia', 17000),
(2020, 'Nokia', 14000); 

SELECT * FROM brands;

-- Write a query to fetch the record of brand whose amount is increasing every year.
SELECT *,
(LEAD (Amount) OVER (PARTITION BY Brand ORDER BY Year)) AS 'LEAD'
FROM brands;

SELECT *,
       (  CASE WHEN Amount < LEAD (Amount) OVER (PARTITION BY Brand ORDER BY Year)
          THEN 1 ELSE 0 END  ) AS Flag
FROM brands;

SELECT *,
       (  CASE WHEN Amount < LEAD (Amount, 1, Amount+1) OVER (PARTITION BY Brand ORDER BY Year)
          THEN 1 ELSE 0 END  ) AS Flag
FROM brands;

-- Final Solution.--
WITH CTE AS
	( SELECT *,
		   (  CASE WHEN Amount < LEAD (Amount, 1, Amount+1) OVER (PARTITION BY Brand ORDER BY Year)
			  THEN 1 ELSE 0 END  ) AS Flag
	FROM brands ) 
SELECT *
FROM brands
WHERE Brand NOT IN  (SELECT Brand FROM CTE WHERE Flag = 0);
