# SQL_RETAIL_SALE_PROJECT
DATA CLEANING PROJECT & DATA EXPLORATION 

## CREATE DATABASE OMBUI_PROJECT
A query to execute the create database

```sql
CREATE DATABASE ombui_project;

```

## CREATING TABLES
I prefer to add the drop table if exists incase there's one

```sql
DROP TABLE IF EXISTS retail_sale;
CREATE TABLE retail_sale
( 
	transactions_id int PRIMARY KEY,
    sale_date date,
	sale_time time,
	customer_id int,
		gender varchar(15),
	age int,
	category varchar(17),
	quantity int,
	price_per_unit float,
	cogs float,
	total_sale float
)
;


```
## SELECTING THE TABLE
Writing a query to see the details in the table in the output

```sql
SELECT * FROM retail_sale;

```


## CHECKING ROWS
To check rows if we impoerted all of them

```sql
SELECT
COUNT(*)
FROM RETAIL_SALE
;

```
-- DATA CLEANING

## CHECKING NULL VALUES

```sql
SELECT*
FROM retail_sale 
WHERE
	transactions_id IS NULL
    OR
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
    COGS IS NULL
    OR
    total_sale IS NULL;

```


## CHECKING REPEATED ROWS
Using ctes to facilitate the process

```sql

 SELECT
    ROW_NUMBER() OVER(
    PARTITION BY transactions_id,sale_date,sale_time,customer_id,gender,age,category,quantity,price_per_unit,cogs,total_sale) as row_num
    FROM retail_sale;
    WITH duplicate_cte AS (
    SELECT*,
    ROW_NUMBER() OVER(
    PARTITION BY transactions_id,sale_date,sale_time,customer_id,gender,age,category,quantity,price_per_unit,cogs,total_sale
    order by transactions_id,sale_date,sale_time,customer_id,gender,age,category,quantity,price_per_unit,cogs,total_sale) as row_num
    FROM retail_sale)
    SELECT*
    FROM duplicate_cte
    WHERE row_num >1;

```


-- DATA EXPLORATION

## Q1. How many sales we have

```sql
SELECT COUNT(*) as total_sale
FROM retail_sale;

```


## Q2.How many unique customers we have

```sql
SELECT COUNT(DISTINCT customer_id) as total_sale
FROM retail_sale;

```
## Q3.How many categories we have

```sql
SELECT DISTINCT category as total_sale
FROM retail_sale;

```


##Q4. Query to show columns for sales made 2023-09-24

```sql
SELECT *
FROM retail_sale
WHERE sale_date='2023-09-24'
;


```


##Q5.Query to show the category is cleaning and quantity sold is more than 4 in NOV 2023

```sql
SELECT category,
SUM(quantity)
FROM retail_sale
	WHERE category='Clothing'
GROUP BY category;

SELECT *
FROM retail_sale
	WHERE category='Clothing'
	AND 
    date_format(sale_date,'%Y-%m')= '2023-11'
    AND
    quantity>=4
    ;

```


##Q6. a query to give total sales for each category

```sql
SELECT
category,
SUM(total_sale)AS net_sale,
count(*) as total_orders
FROM retail_sale;

```


##Q7. query to find average age of customers who purchased from beauty category

```sql
SELECT
ROUND(AVG(age) ,2)
FROM retail_sale
WHERE category='beauty';

```

##Q8. a query to find transactions where total sales is greater than 1000

```sql
SELECT*
FROM retail_sale
WHERE total_sale>1000 ;

```

##Q9. a query to find total transactions made by each gender to each category

```sql
SELECT
category,
gender,
COUNT(*) AS total_trans
FROM retail_sale
GROUP BY category,gender
ORDER BY 1;

```


##Q10. calculate average sale for each month,best selling month in each year

```sql
SELECT
	YEAR(sale_date) AS year,
	MONTH(sale_date) AS month,
	ROUND(AVG(total_sale) ,3) AS avg_sale
    FROM retail_sale
GROUP BY 1,2
ORDER BY 1,3 DESC;

SELECT
year,
month,
avg_sale,
RANK() OVER (PARTITION BY year order by AVG_SALE desc) as ranking
FROM (
SELECT
	YEAR(sale_date) AS year,
	MONTH(sale_date) AS month,
	ROUND(AVG(total_sale) ,3) AS avg_sale
    FROM retail_sale
GROUP BY 1,2) AS monthly_sales
ORDER BY 1 asc,avg_sale desc;

SELECT*
FROM (
		SELECT
	year,
	month,
	avg_sale,
	RANK() OVER (PARTITION BY year order by AVG_SALE desc) as ranking
FROM (
SELECT
	YEAR(sale_date) AS year,
	MONTH(sale_date) AS month,
	ROUND(AVG(total_sale) ,3) AS avg_sale
    FROM retail_sale
GROUP BY 1,2) AS monthly_sales
ORDER BY 1 asc,avg_sale desc) AS TABLE_1
WHERE ranking=1;

```


##Q11. query to find top 5 customers based on highest total sales

```sql
ELECT
customer_id,
SUM(total_sale) AS TOTAL_SALE
FROM retail_sale
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;

```

 ##Q12. query to find number of unique customers who purchased items from each category

```sql
SELECT
category,
COUNT(distinct customer_id) as unique_customers
FROM retail_sale
GROUP BY category;

```


##Q13. create shift and number of orders

```sql
with hourly_sales
AS(
SELECT*,
CASE
	 WHEN hour(sale_time) < 12 THEN 'morning'
     WHEN hour(sale_time) between 12 and 17 then 'afternoon'
     ELSE 'evening'
     END AS shift
FROM retail_sale
)
SELECT
shift,
COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift


```







































