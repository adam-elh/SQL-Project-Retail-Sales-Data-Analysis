# SQL-Project-Retail-Sales-Data-Analysis

```sql

-- Personal Study - SQL Project Retail Sales Dataset Data Analysis

-- Create Table Query

DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);

-- Exploratory Data Analysis

SELECT * FROM retail_sales; 

SELECT COUNT(*) FROM retail_sales; -- 2000 transactions

SELECT * FROM retail_sales
WHERE transactions_id IS NULL; -- 0 instances

SELECT * FROM retail_sales
WHERE sale_time IS NULL; -- 0 instances

SELECT * FROM retail_sales
WHERE  IS NULL; -- 0 instances

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL 
	OR 
	sale_time IS NULL 
	OR 
	customer_id IS NULL 
	OR 
    gender IS NULL 
	OR 
	age IS NULL 
	OR 
	category IS NULL 
	OR 
    quantity IS NULL 
	OR 
	price_per_unit IS NULL 
	OR 
	cogs IS NULL; -- 13 instances

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL 
	OR 
	sale_time IS NULL 
	OR 
	customer_id IS NULL 
	OR 
    gender IS NULL 
	OR 
	age IS NULL 
	OR 
	category IS NULL 
	OR 
    quantity IS NULL 
	OR 
	price_per_unit IS NULL 
	OR 
	cogs IS NULL; -- data cleaning

SELECT COUNT(*) FROM retail_sales; -- 1987

-- Basic Questions (Business Problems)

-- 1. How many sales are there?

SELECT COUNT(*) FROM retail_sales; -- 1987

-- 2. How many unique customers purchased items?

SELECT COUNT(DISTINCT(customer_id)) as unique_customers
FROM retail_sales; -- 155 Unique customers

-- 3. How many unique categories of items are there?

SELECT DISTINCT(category)
FROM retail_sales; -- 3 categories

-- 4. Create a SQL query to retrieve all columns for sales made on '2022-11-05

SELECT * 
FROM retail_sales
WHERE sale_date = '2022-11-05'; -- 11 instances

-- 5. Create a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022.

SELECT * 
FROM retail_sales
WHERE 
    category = 'Clothing'
	AND 
	quantity >= 4
	AND
	TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'; -- 0 instances

-- 6. Create a SQL query to calculate the total sales (total_sale) for each category.

SELECT 
     category, 
	 SUM(total_sale) as net_sales, 
	 COUNT(*) as total_orders
FROM retail_sales
GROUP BY category;

-- 7. Create a SQL query to find the average age of customers who purchased items from the 'Beauty' category.

SELECT ROUND(AVG(age)) 
FROM retail_sales
WHERE category = 'Beauty'; -- 40

-- 8. Create a SQL query to find all transactions where the total_sale is greater than 1000.

SELECT * FROM retail_sales
WHERE total_sale > 1000; -- 306 instances

-- 9. Create a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.

SELECT 
     category,
	 gender,
	 COUNT(*) as total
FROM retail_sales
GROUP BY category, gender ORDER BY total DESC;

-- 10. Create a SQL query to calculate the average sale for each month. Find out best selling month in each year.

SELECT * FROM 
(
SELECT 
     EXTRACT(YEAR FROM sale_date) as year,
	 EXTRACT(MONTH FROM sale_date) as month,
	 ROUND(AVG(total_sale)) as avg_sales,
	 RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY year, month 
ORDER BY year, avg_sales DESC
) as t1
WHERE rank = 1;

-- 11. Create a SQL query to find the top 5 customers based on the highest total sales.

SELECT 
     customer_id,
	 SUM(total_sale) as total
FROM retail_sales
GROUP BY customer_id ORDER BY total DESC LIMIT 5;

-- 12. Create a SQL query to find the number of unique customers who purchased items from each category.

SELECT 
	 category,
     COUNT(DISTINCT(customer_id)) as unique_customer
FROM retail_sales
GROUP BY category;

-- 13. Create a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17).

WITH hourly_sales AS (
SELECT 
     *,
	 CASE 
	    WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
		WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
		ELSE 'Evening'
	 END as shift
FROM retail_sales
)
SELECT
     shift,
	 COUNT(*) as total_orders
FROM hourly_sales 
GROUP BY shift;


```
