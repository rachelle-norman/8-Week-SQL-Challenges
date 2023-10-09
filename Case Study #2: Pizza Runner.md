# 🍕 Pizza Runner - Case Study #2

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/ec8ed386-9efb-4eed-baaa-ee44bc7546ae" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Data Cleaning and Transformation](#data-cleaning-and-transformation)
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

## 🧼 Data Cleaning and Transformation

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

**#1: How many pizzas were ordered?**

````sql
SELECT
	COUNT(*) AS number_of_pizzas
FROM
	customer_orders
;
````
#### Steps:
- Run a `COUNT(*)` function on the `customer_orders` table to count how many instances there are.

#### Answer:
| number_of_pizzas |
| --- |
| 14 |

There were 14 pizzas ordered total.

***

**#2: How many unique customer orders were made?**

````sql
SELECT
	COUNT(DISTINCT c.order_id) AS number_of_orders
FROM
	customer_orders c
;
````
#### Steps:
- Run a `COUNT (DISTINCT)` function on the `c.order_id` column in the `customer_orders` table to count only unique order IDs and not any duplicates.

#### Answer:

| number_of_orders |
| --- |
| 10 |

There were 10 unique customer orders made.

***

**#3: How many successful orders were delivered by each runner?**

````sql
SELECT
	r.runner_id,
	COUNT(r.order_id) AS number_of_orders
FROM
	runner_orders r
WHERE
	pickup_time IS NOT null
GROUP BY
	r.runner_id
ORDER BY
	r.runner_id
;
````
#### Steps:
- Run a `COUNT` function on the `r.order_id` column in the `runner_orders` table to count how many orders were made by the runners
- Filter the results where the `r.pickup_time` isn't `NULL` as this indicates an order cancellation.
- Group the results by the `r.runner_id` to aggregate the number of orders for each runner.
- Order the results by the `r.runner_id` for easier readability.

#### Answer:

| runner_id | number_of_orders |
| --- | --- |
| 1 | 4 |
| 2 | 3 |
| 3 | 1 |

Runner 1 delivered 4 orders.
Runner 2 delivered 3 orders.
Runner 3 delivered 1 order.

***

**#4: How many of each type of pizza was delivered?**

````sql
WITH delivered_orders AS (
		SELECT 
			r.order_id
		FROM 
			runner_orders r
		WHERE
			r.pickup_time IS NOT NULL
)

--- end of cte ---

SELECT
	p.pizza_name,
	COUNT(c.pizza_id) AS delivered_count
FROM
	pizza_names p
		JOIN customer_orders c
			ON p.pizza_id=c.pizza_id
		JOIN delivered_orders d
			ON c.order_id=d.order_id
GROUP BY
	p.pizza_name
ORDER BY
	p.pizza_name
;
````
#### Steps:
- Create a CTE that stores the order IDs of all orders that were delivered by using the `r.pickup_time` to filter out any cancelled orders.
- Run a `COUNT` function to find the number of each type of pizza that was ordered.
- Use a `JOIN` clause to combine the `pizza_names` table to the `customers` table to retrieve the pizza name for each pizza ID.
- Use a 2nd `JOIN` to further combine the tables with the CTE to only show results if the order was actually delivered.
- Group and order the results by the `p.pizza_name`.

#### Answer:

| pizza_name | delivered_count |
| --- | --- |
| Meat Lovers | 9 |
| Vegetarian | 3 |


The Meat Lovers pizza was delivered 9 times.
The Vegetarian pizza was delivered 3 times.

***

**#5: How many Vegetarian and Meat Lovers were ordered by each customer?**

````sql
SELECT
	c.customer_id,
	p.pizza_name,
	COUNT(c.pizza_id) AS order_count
FROM
	customer_orders c
		JOIN pizza_names p
			ON c.pizza_id=p.pizza_id
GROUP BY
	c.customer_id,
	p.pizza_name
ORDER BY 
	c.customer_id
;
````
#### Steps:
- Use a `COUNT` function to count the number of pizzas ordered.
- Use a `JOIN` clause on the `customer_orders` table and the `pizza_names` table to retrieve the names of the pizzas. 
- Group the results by the `c.customer_id` and `p.pizza_name` columns and order them by the `c.customer_id`.
  
#### Answer:
| customer_id | pizza_name | order_count |
| 101 | Meat Lovers | 2 |
| 101 | Vegetarian | 1 |
| 102 | Meat Lovers | 2 |
| 102 | Vegetarian | 1 |
| 103 | Meat Lovers | 3 |
| 103 | Vegetarian | 1 |
| 104 | Meat Lovers | 3 |
| 105 | Vegetarian | 1 |

Customer 101 and 102 both ordered 2 Meat Lovers and 1 Vegetarian pizza.
Customer 103 ordered 3 Meat Lovers and 1 Vegetarian pizza.
Customer 104 ordered 3 Meat Lovers pizzas.
Customer 105 ordered 1 Vegetarian pizza.

***

**#6: What was the maximum number of pizzas delivered in a single order?**

````sql
SELECT
	MAX(number_of_pizzas) AS max_number_of_pizzas
FROM (
--- subquery to find the amount of pizzas per order ---
	SELECT
		c.order_id,
		COUNT(c.pizza_id) AS number_of_pizzas
	FROM
		customer_orders c
	GROUP BY
		c.order_id
	) AS pizza_per_order
;
````
#### Steps:
- Since it's not possible to nest aggregate functions, use a `MAX` function on a `COUNT` function alias provided in a subquery used to count the number of pizzas per order.

#### Answer:
| max_number_of_pizzas |
| --- |
| 3 |

The highest number of pizzas in a single order is 3.

***

**#7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#8: How many pizzas were delivered that had both exclusions and extras?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#9: What was the total volume of pizzas ordered for each hour of the day?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#10: What was the volume of orders for each day of the week?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Runner and Customer Experience

**#1: How many runners signed up for each 1 week period? (i.e. week starts `2021-01-01`**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: Is there any relationship between the number of pizzas and how long the order takes to prepare?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: What was the average distance travelled for each customer?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: What was  the difference between the longest and shortest delivery times for all orders?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#6: What was the average speed for each runner for each delivery and do you notice any trend for these values?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#7: What is the successful delivery percentage for each runner?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Ingredient Optimization

**#1: What are the standard ingredients for each pizza?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What was the most commonly added extra?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: What was the most common exclusion?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: Generate an order item for each record in the `customer_orders` table in the format of one of the following:
	- Meat Lovers
 	- Meat Lovers (Exclude Beef)
  	- Meat Lovers (Extra Bacon)
   	- Meat Lovers (Exclude Cheese, Bacon, Extra Mushrooms and Peppers)**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: Generate an alphabetically ordered, comma separated ingredient list for each pizza order from the `customer_orders` table and add a `2x` in front of any relevant ingredients.
	- For example: "Meat Lovers: 2x Bacon, Beef, ..., Salami"
 
````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#6: What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Pricing and Ratings

**#1: If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes, how much money has Pizza Runner made so far if there are no delivery fees?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What if there was an additional $1 charge for any pizza extras?
	- Add cheese is $1 extra.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: The Pizza Runner team now wants to add an additional ratings syustem that allows customers to rate their runner. How would you design an additional table for this new dataset? Generate a schema for this new table and insert your own data for ratings for each successful customer between 1 to 5.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: Using your newly generated table, can you join all of the information together to form a table which has the following information for successful deliveries?
	- `customer_id`, `order_id`, `runner_id`, `rating`, `order_time`, `pickup_time`, time between order and pickup, delivery duration, average speed, and total number of pizzas**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: If a Meat Lovers pizza was $12 and Vegetarian was $10 with fixed prices with no cost for extras and each runner is paid $0.30 per kilometer travelled, how much money does Pizza Runner have left over after these deliveries?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Bonus DML Challenges

**#1: If Danny wants to expand his range of pizzas, how would this impact the existing data design? Write an `INSERT` statement to demonstrate what would happen if a new `Supreme` pizza with all the toppings was added to the Pizza Runner menu.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***
