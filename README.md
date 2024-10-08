# SQL-Projects
These projects demonstrate my proficiency in SQL and my capacity to analyze complex data. They exhibit my abilities in data investigation, visualization, and analysis.

## Analyzing Employee Trends
SQL Query
```
use hr_data;
--- To read Data ---
SELECT * FROM hrdata;

--- 1. Count the number of employees in each department---
SELECT department, COUNT(*) AS employee_count
FROM hrdata
GROUP BY department;

--- 2.Calculate the average age for each department ---
SELECT department, AVG(age) AS average_age
FROM hrdata
GROUP BY department;

---- 3. Identify the most common job roles in each department ---
SELECT department, job_role, COUNT(*) AS role_count
FROM hrdata
GROUP BY department, job_role
ORDER BY department, role_count DESC;

--- 4. Calculate the average job satisfaction for each education level ---
SELECT education, AVG(job_satisfaction) AS average_satisfaction
FROM hrdata
GROUP BY education;

--- 5.Determine the average age for employees with different levels of job satisfaction ---
SELECT job_satisfaction, AVG(age) AS average_age
FROM hrdata
GROUP BY job_satisfaction;

--- 6. Calculate the attrition rate for each age band --
SELECT age_band, SUM(CASE WHEN attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*) * 100 AS attrition_rate
FROM hrdata
GROUP BY age_band;

--- 7. Identify the departments with the highest and lowest average job satisfaction ---
SELECT department, AVG(job_satisfaction) AS average_satisfaction
FROM hrdata
GROUP BY department
ORDER BY average_satisfaction DESC, department
LIMIT 1;

--- 8. Find the age band with the highest attrition rate among employees with a specific education level---
SELECT education, age_band, SUM(CASE WHEN attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*) * 100 AS attrition_rate
FROM hrdata
GROUP BY education, age_band
ORDER BY attrition_rate DESC
LIMIT 1;

--- 9.Find the education level with the highest average job satisfaction among employees who travel frequently ---
SELECT education, AVG(job_satisfaction) AS average_satisfaction
FROM hrdata
WHERE business_travel = 'Travel_Frequently'
GROUP BY education
ORDER BY average_satisfaction DESC
LIMIT 3;

--- 10. Identify the age band with the highest average job satisfaction among married employees ----
SELECT age_band, AVG(job_satisfaction) AS average_satisfaction
FROM hrdata
WHERE marital_status = 'Married'
GROUP BY age_band
ORDER BY average_satisfaction DESC
LIMIT 1;
```
## Project Description
This project involved performing extensive analysis on HR employee data to uncover insights and trends. The data contained information on employees across departments, including demographics, job roles, satisfaction, attrition and more.

I utilized SQL to explore the data and answer key business questions to derive actionable insights.

## Tasks Performed
- Performed general data exploration and validation checks on the HR dataset
- Wrote SQL queries to analyze employee counts, averages, distributions across various dimensions like department, age, education etc.
- Identified top job roles and satisfaction levels by department to understand departmental trends
- Calculated employee attrition rates by age band to pinpoint high risk groups
- Analyzed factors related to attrition like age, education, job satisfaction to uncover drivers
- Compared averages and aggregates across different employee segments and criteria
- Identified top and bottom performers on key metrics like satisfaction, attrition based on criteria
- Used SQL techniques like JOINs, GROUP BY, HAVING, CASE statements for complex analysi
- Organized and presented findings in a clear format for consumption by leadership

## Exploring Trends in Automotive Industry
SQL Query
```
use cars_info;
--- To read Data ---
SELECT * FROM car_info;

--- 1. Calculate the average selling price for cars with a manual transmission and owned by the first owner, for each fuel type. ---
SELECT fuel, AVG(selling_price) AS avg_selling_price
FROM car_info
WHERE transmission = 'Manual' AND owner = 'First Owner'
GROUP BY fuel;

--- 2. Find the top 3 car models with the highest average mileage, considering only cars with more than 5 seats.---
SELECT Name, AVG(mileage) AS avg_mileage
FROM car_info
WHERE seats > 5
GROUP BY Name
ORDER BY avg_mileage DESC
LIMIT 3;

--- 3. Identify the car models where the difference between the maximum and minimum selling prices is greater than $10,000. ---
SELECT Name
FROM car_info
GROUP BY Name
HAVING MAX(selling_price) - MIN(selling_price) > 10000;

--- 4. Retrieve the names of cars with a selling price above the overall average selling price and a mileage below the overall average mileage. ---
SELECT Name
FROM car_info
WHERE selling_price > (SELECT AVG(selling_price) FROM car_info)
    AND mileage < (SELECT AVG(mileage) FROM car_info);
    
--- 5. Calculate the cumulative sum of the selling prices over the years for each car model. ---
SELECT Name, year, selling_price, 
       SUM(selling_price) OVER (PARTITION BY Name ORDER BY year) AS cumulative_sum
FROM car_info;

--- 6. Identify the car models that have a selling price within 10% of the average selling price. ---
SELECT Name, selling_price
FROM car_info
WHERE selling_price BETWEEN (SELECT AVG(selling_price) * 0.9 FROM car_info) AND (SELECT AVG(selling_price) * 1.1 FROM car_info);

--- 7. Calculate the exponential moving average (EMA) of the selling prices for each car model, considering a smoothing factor of 0.2. ---
SELECT Name, year, selling_price,
       AVG(selling_price) OVER (PARTITION BY Name ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ema_selling_price
FROM car_info;

--- 8. Identify the car models that have had a decrease in selling price from the previous year. ---
SELECT Name, year, selling_price
FROM (
    SELECT Name, year, selling_price,
           LAG(selling_price) OVER (PARTITION BY Name ORDER BY year) AS previous_year_price
    FROM car_info
) AS price_changes
WHERE selling_price < previous_year_price;

--- 9. Retrieve the names of cars with the highest total mileage for each transmission type. ---
WITH TotalMileage AS (
    SELECT Name, transmission, SUM(km_driven) AS total_mileage
    FROM car_info
    GROUP BY Name, transmission
)
SELECT Name, transmission, total_mileage
FROM TotalMileage
WHERE (transmission, total_mileage) IN (
    SELECT transmission, MAX(total_mileage)
    FROM TotalMileage
    GROUP BY transmission
);

--- 10. Find the average selling price per year for the top 3 car models with the highest overall selling prices. ---
WITH RankedSellingPrices AS (
    SELECT Name, selling_price,
           RANK() OVER (PARTITION BY Name ORDER BY selling_price DESC) AS price_rank
    FROM car_info
)
SELECT Name, year, AVG(selling_price) AS avg_selling_price_per_year
FROM car_info
WHERE Name IN (
    SELECT Name
    FROM RankedSellingPrices
    WHERE price_rank <= 3
)
GROUP BY Name, year;
```
## Project Description
This project involved performing extensive analysis on a database of car sales information to uncover insights into pricing trends, model performance, and more. The data contained details on car models, pricing history, mileage, transmission types and other attributes.

I utilized SQL to explore the data and derive insights to guide pricing, inventory and marketing strategies.

## Tasks Performed
- Performed general data exploration and validation checks on the cars dataset
- Analyzed average selling prices by various dimensions like transmission, fuel type, ownership etc. to reveal pricing patterns
- highest mileage models to understand durability and maintenance costs
- Computed price variability within and across models to guide pricing strategy
- Compared pricing and mileage metrics vs overall averages to find outliers
- Generated cumulative price trends over time and YoY comparisons for price forecasting
- Computed complex iterative analytics like moving averages for price smoothing
- Summarized total mileage by transmission for maintenance and repair planning
- Ranked models by selling price and analyzed historical averages for top models
- Used SQL techniques like WINDOW functions, CTEs, JOINs for advanced analysis
- Presented findings in a clear format for consumption by leadership and stakeholders

Overall, this project enabled me to showcase my SQL skills to derive data-driven insights from car sales data to support key business decisions and strategy.

## Call Center Data Cleaning
SQL Query

```
use call_centerdata;
SELECT * FROM call_center;

---- Data cleaning ---
SET SQL_SAFE_UPDATES = 0;

--- changing date format---
UPDATE call_center 
SET call_timestamp = str_to_date(call_timestamp, "%m/%d/%Y");
--- updating empty values to NUll ----
UPDATE call_center 
SET csat_score = NULL
WHERE csat_score = '';
SET SQL_SAFE_UPDATES = 1;
SELECT * FROM call_center LIMIT 10;

--- Count of num of rows and num of columns in table ---
SELECT COUNT(*) AS num_rows FROM call_center;
SELECT COUNT(*) AS num_columns
FROM information_schema.columns
WHERE table_name = 'call_center' AND table_schema = 'call_centerdata';

--- to select distinct values in each column ----
SELECT DISTINCT sentiment FROM call_center;
SELECT DISTINCT city FROM call_center;
SELECT DISTINCT call_center FROM call_center;

--- to count and % from total of each distinct values in a column in call_center table ---
SELECT city,
  COUNT(*) AS count,
  COUNT(*) * 100.0 / (SELECT COUNT(*) FROM call_center) AS percentage
FROM call_center
GROUP BY city;
  
--- Call count of=n each day---
SELECT DAYNAME(call_timestamp) AS day_of_week,
COUNT(*) AS call_count
FROM call_center
GROUP BY day_of_week
ORDER BY call_count DESC;

---- Calculations ---
SELECT
  MIN(`call duration in minutes`) AS min_duration,
  MAX(`call duration in minutes`) AS max_duration,
  AVG(`call duration in minutes`) AS avg_duration
FROM call_center;

SELECT
  MIN(csat_score) AS min_csat,
  MAX(csat_score) AS max_csat,
  ROUND(AVG(csat_score), 2) AS avg_csat
FROM call_center
WHERE csat_score <> 0; --- where cast_score is not equal to 0 ---

SELECT call_center, response_time, COUNT(*) as count
From call_centerdata.call_center GROUP BY 1,2  ORDER BY 1,3 DESC;

SELECT
  DATE(call_timestamp) AS call_day,
  MAX(`call duration in minutes`) AS max_call_duration
FROM call_center
GROUP BY call_day
ORDER BY call_day;
```

## Project Description
This project involved an analysis of call center data to gain insights into call volume, customer satisfaction, and service performance. The data was contained in a MySQL database table called call_center within the call_centerdata schema.

## Tasks Performed
- Imported the call_center table data into a MySQL database for analysis
- Performed initial data cleaning Changed date format to a standard YYYY-MM-DD format Updated blank values for csat_score to NULL
- Generated table summary statistics Counted total number of rows Counted total number of columns
- Analyzed distinct values and distributions Found distinct values for sentiment, city, and call_center columns Calculated value counts and percentages by city
- Analyzed call volume Calculated call counts by day of week Identified maximum call duration by day
- Analyzed customer satisfaction Calculated minimum, maximum, and average csat scores Removed scores of 0 from average calculation
- Analyzed service performance Calculated response time counts by call center Ordered to identify best and worst performing centers

## About SQL
SQL (Structured Query Language) is a programming language for interacting with relational databases. Key SQL capabilities:

- CRUD operations - create, read, update, delete data
- Joins - combine data from multiple tables
- Aggregate functions - summations, counts, averages on data
- Subqueries - nested queries for advanced operations
- Stored procedures - reusable routines for logic
- Window functions - analytics functions for rankings etc.
For these examples, I used Microsoft SQL Server Management Studio.
