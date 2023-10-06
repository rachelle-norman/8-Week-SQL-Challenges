# 🍕 Pizza Runner - Case Study #2

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/ec8ed386-9efb-4eed-baaa-ee44bc7546ae" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Data Cleaning and Manipulation](#data-cleaning-and-manipulation)
- [Questions and Solutions](#questions-and-solutions)
	- [Pizza Metrics](#pizza-metrics)
	- [Runner and Customer Experience](#runner-and-customer-experience)
   	- [Ingredient Optimization](#ingredient-optimization)
   	- [Pricing and Ratings](#pricing-and-ratings)
   	- [Bonus DML Challenges](#bonus-dml-challenges)

All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-2/) website.

***

## Objective

Danny launched Pizza Runner, an innovative pizza delivery service inspired by 80s retro styling, aiming to combine his love for pizza with an Uber-like model, recruiting runners for deliveries and developing a mobile app but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

***

## Entity Relationship Diagram

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/5f275415-1782-47bc-b289-e46b68184a59" alt="Image" width="650">


***

## 🧼 Data Cleaning & Transformation

## Table: customer_orders

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/3fadfd92-772f-4b51-92d0-fa27876b3219" alt="Image" width="750">
<br> </br>

**Issues:** Null values in the `exclusions` and `extras` columns.

**Actions to Clean:**
````sql
SELECT
	*
FROM
	customer_orders;

UPDATE customer_orders
SET extras = ' '
WHERE extras IS NULL
	OR extras = 'null'
;

UPDATE customer_orders
SET exclusions = ' '
WHERE exclusions IS NULL
	OR exclusions = 'null'
;
````
- Run a `SELECT *` statement to select all columns from the tables to see the data and the data types to identify where the issues in the table lie.
- Since the data type for both the `exclusions` and `extras` tables is 'VARCHAR(4)`, update the null values to ' ' since it's also a string.
- Highlight and run the `SELECT *` statement again after running the `UPDATE` statements to ensure the data accurately reflects the changes.

Here is the updated table:

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/072bf428-a419-4947-9744-a06b031bd0bb" img="Image" width="750">
<br> </br>

## Table: runner_orders

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/26ace5d1-2085-4679-8862-77a660609dd5" img="Image" width="750">
<br> </br>

**Issues:** Mismatched data types, inconsistent values

**Actions to Clean:**
````sql
SELECT 
	*
FROM
	runner_orders;

UPDATE runner_orders
	SET pickup_time = NULL
	WHERE pickup_time ='null';
	
UPDATE runner_orders
	SET			
		distance = CASE
			WHEN distance LIKE '%km%' THEN TRIM('%km%' FROM distance)
			WHEN distance = 'null' THEN '0'
			ELSE distance
		END,
			
		duration = CASE
			WHEN duration LIKE '%mins' THEN TRIM('%mins' FROM duration)
			WHEN duration LIKE '%minute' THEN TRIM('%minute' FROM duration)	
			WHEN duration LIKE '%minutes' THEN TRIM('%minutes' FROM duration)
			WHEN duration LIKE 'null' THEN '0'
			ELSE duration
			END,
			
		cancellation = CASE
			WHEN cancellation IS NULL THEN ' '
			WHEN cancellation = 'null' THEN ' '
			ELSE cancellation
			END
;
````
- Set the `pickup_time` `'null'` strings to actual `NULL` values. Note: Since we're changing the data types later and blank strings cannot exist in timestamp values, it's probably best for this one to stay as a `NULL`.
- Use `CASE` functions to`TRIM` the letters out of the `distance` and `duration` columns and set the `'null'` string values to `'0'` to make it easier to `CAST` to another data type later.
- Use another `CASE` function to change `NULL` values and `'null'` strings to blank space.
<br> </br>  
````sql
CREATE TABLE runner_orders_updated AS 
	SELECT
		CAST(order_id AS bigint),
		runner_id,
		CAST(pickup_time AS timestamp),
		CAST(distance AS float),
		CAST(duration AS int),
		cancellation
	FROM
		runner_orders;

SELECT 
	*
FROM
	runner_orders_updated;

DROP TABLE runner_orders;

ALTER TABLE runner_orders_updated
RENAME TO runner_orders
;
````
- Create a new table and insert the columns from the original table, using a `CAST` function on the columns where we want to update the data type.
- `CAST` the `order_id` column as `bigint` because we might have more orders later on and might need an integer data type with more space and the `pickup_time` column as `timestamp` so we can easily make calculations with other date columns in the schema.
- `CAST` the `distance` column as a floating-point number since the original column has decimals and the `duration` column as `integer`.
- Run a `SELECT *` statement on the new table to ensure that all of the columns from the original table are in the new table as the proper data type.
- Delete the original `runner_orders` table and rename the new, correctly updated table to `runner_orders`.
- Rerun the `SELECT *` from the beginning of the query using the original `runner_orders` table name to ensure that all changes have been reflected.

Here is the updated table:

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/3bfdbdc0-b4db-4ba4-a16c-bb545c17c784" img="Image" width="750">

***

## Table: pizza_names

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/4d99a83a-eb1f-44d5-b761-72742995626b" alt="Image" width="250">
<br> </br>

**Issues:** Incorrect spacing

**Actions to Clean:**
````sql
UPDATE pizza_names
SET pizza_name = 'Meat Lovers'
WHERE pizza_name = 'Meatlovers'
````
- Run a quick `UPDATE` statement to correct formatting in the `pizza_name` column.

Here are the changes reflected:

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/a668d1e8-0fd4-46ae-863e-3e71c16caefc" alt="Image" width="250">

***

## Questions and Solutions

## Pizza Metrics

**#1: Question goes here**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here
***

## Runner and Customer Experience

**#1: Question goes here**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here
***

## Ingredient Optimization

**#1: Question goes here**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here
***

## Pricing and Ratings

**#1: Question goes here**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here
***

## Bonus DML Challenges

**#1: Question goes here**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here
***
