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
SET extras = ''
WHERE extras IS NULL
	OR extras = 'null'
;

UPDATE customer_orders
SET exclusions = ''
WHERE exclusions IS NULL
	OR exclusions = 'null'
;
````
- Run a `SELECT *` statement to select all columns from the tables to see the data and the data types to identify where the issues in the table lie.
- Since the data type for both the `exclusions` and `extras` tables is 'VARCHAR(4)`, update the null values to blank values.
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

- Runner 1 delivered 4 orders. 
- Runner 2 delivered 3 orders.
- Runner 3 delivered 1 order.

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


- The Meat Lovers pizza was delivered 9 times.
- The Vegetarian pizza was delivered 3 times.

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
| --- | --- | --- |
| 101 | Meat Lovers | 2 |
| 101 | Vegetarian | 1 |
| 102 | Meat Lovers | 2 |
| 102 | Vegetarian | 1 |
| 103 | Meat Lovers | 3 |
| 103 | Vegetarian | 1 |
| 104 | Meat Lovers | 3 |
| 105 | Vegetarian | 1 |

- Customer 101 and 102 both ordered 2 Meat Lovers and 1 Vegetarian pizza.
- Customer 103 ordered 3 Meat Lovers and 1 Vegetarian pizza.
- Customer 104 ordered 3 Meat Lovers pizzas.
- Customer 105 ordered 1 Vegetarian pizza.

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
WITH delivered_orders AS (
	SELECT
		r.order_id
	FROM
		runner_orders r
	WHERE
		r.pickup_time IS NOT NULL
),

--- end of cte

pizza_numbers_per_cust AS (
	SELECT 
		c.customer_id,
		SUM(
			CASE
				WHEN length(c.exclusions) != 0 OR length(c.extras) != 0 THEN 1
				ELSE 0
				END) AS modded_pizza_count,
	
		SUM(
			CASE
				WHEN length(c.exclusions) = 0 AND length(c.extras) = 0 THEN 1
				ELSE 0
				END) AS original_pizza_count
	
	FROM
		customer_orders c
			JOIN delivered_orders d
				ON c.order_id=d.order_id
	GROUP BY
		c.customer_id

)

--- end of cte ---

SELECT
	p.customer_id,
	p.modded_pizza_count,
	p.original_pizza_count
FROM
	pizza_numbers_per_cust p
GROUP BY
	p.customer_id,
	p.modded_pizza_count,
	p.original_pizza_count
ORDER BY
	p.customer_id
;
````
#### Steps:
- Create a CTE selecting all order IDs from the `r.runner_orders` table since this table holds the data to tell us whether or not an order was actually delivered.
- Create a second CTE using `SUM` with `CASE WHEN` to count two categories: modified pizzas with values in the `exclusions` or `extras` columns and original pizzas with no modifications.
- Group the results by `customer_id` to obtain the count for each customer in both categories.
- `JOIN` the `delivered_orders` CTE to the `pizza_numbers_per_cust` to only look at data from orders that were fulfilled.
- In the main query, select all of the newly-created columns from the 2nd CTE to display the final answer.
  
#### Answer:
| customer_id | modded_pizza_count | original_pizza_count |
| --- | --- | --- |
| 101 | 0 | 2 |
| 102 | 0 | 3 |
| 103 | 3 | 0 |
| 104 | 2 | 1 |
| 105 | 1 | 0 |

- Customer 101 ordered 2 pizzas without any modifications.
- Customer 102 ordered 3 pizzas without any modifications.
- Customer 103 ordered 3 pizzas with at least 1 change.
- Customer 104 ordered 2 pizzas with at least 1 change and 1 pizza without any modifications.
- Customer 105 ordered 1 pizza with at least 1 change.

***

**#8: How many pizzas were delivered that had both exclusions and extras?**

````sql
WITH delivered_orders AS (
	SELECT
		r.order_id
	FROM
		runner_orders r
	WHERE
		r.pickup_time IS NOT NULL
)

--- end of cte

SELECT
	SUM(CASE
		WHEN length(c.exclusions) != 0 AND length(c.extras) != 0 THEN 1
		ELSE 0
	END) AS pizzas_w_both_changes
		
FROM
	customer_orders c
		JOIN delivered_orders d
			ON c.order_id=d.order_id
;
````
#### Steps:
- - Create a CTE selecting all order IDs from the `r.runner_orders` table since this table holds the data to tell us whether or not an order was actually delivered.
- In the main query, use a `SUM` function in conjunction with a `CASE WHEN` statement to count how many pizzas had values in both the `exclusions` and the `extras` columns. 
- `JOIN` the `delivered_orders` CTE to the `pizza_numbers` to only look at data from orders that were fulfilled.

#### Answer:
| pizzas_w_both_changes |
| --- |
| 1 |

***

**#9: What was the total volume of pizzas ordered for each hour of the day?**

````sql
SELECT
	DATE_PART('hour', c.order_time) AS hour,
	COUNT(c.order_id) AS hourly_count
FROM
	customer_orders c
GROUP BY
	hour
ORDER BY
	hour
;
````
#### Steps:
- Use a `DATE_PART` function to extract the hour out of the `order_time` column.
- Use a `COUNT` function to count the number of orders.
- Group the results by hour to get an hourly order count.

#### Answer:
| hour | hourly_order_count|
| --- | --- |
| 11 | 1 |
| 13 | 3 |
| 18 | 3 |
| 19 | 1 |
| 21 | 3 |
| 23 | 3 | 

1pm, 6pm, 9pm, and 11pm all had 3 pizzas ordered during that hour while 11am and 7pm only had 1 pizza ordered during each hour.

***

**#10: What was the volume of orders for each day of the week?**

````sql
SELECT
	TO_CHAR(c.order_time, 'Day') AS day_of_week,
	COUNT(c.order_id) AS pizzas_ordered
FROM
	customer_orders c
GROUP BY
	day_of_week
;
````
#### Steps:
- Extract the name of the day of the week by using the `TO_CHAR` function on the `c.order_time` column.
- Use a `COUNT` function on the `c.order_id` column to count the number of orders.
- Group the results by day of the week.

#### Answer:

| day_of_week | pizzas_ordered |
| --- | --- |
| Wednesday | 5 |
| Thursday | 3 |
| Friday | 1 |
| Saturday | 5 |

- 5 pizzas were ordered on a Wednesday and a Saturday each.
- 3 pizzas were ordered on a Thursday.
- Only 1 pizza was ordered on a Friday.

***

## Runner and Customer Experience

**#1: How many runners signed up for each 1 week period? (i.e. week starts `2021-01-01`)**

````sql
SELECT
    ROUND(
	(EXTRACT(DOY FROM r.registration_date) - EXTRACT(DOY FROM '2021-01-01'::date)) / 7 + 1,0) AS week_number,
    COUNT(r.runner_id) AS runner_registrations
FROM
    runners r
GROUP BY
    week_number
ORDER BY
	week_number
;
````
#### Steps:

**Note: Initially I was going to just use a DATE_PART function but it returned 53 in the `week_number` column instead of 1 for `2021-01-01` so I had to find a work around to get thet data returned how I wanted it.**

- Extract the day of the year (DOY) from the `r.registration_date` column and from `2021-01-01` and subtract it from the `r.registration_date` DOY to find the difference between the two.
- Divide the equation by 7 and add 1 to calculate what week the `r.registration_date` falls in and round it up to the closest whole number. 
- Use a `COUNT` function on the `r.runner_id` column to find how many runners signed up.
- `GROUP` the results by the `week_number` created in the `ROUND` function.

#### Answer:
| week_number | runner_registrations |
| --- | --- |
| 1 | 2 |
| 2 | 1 |
| 3 | 1 |

Week 1 had 2 registrations while Week 2 and Week 3 both had 1 registration each.

***

**#2: What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

````sql
SELECT
	r.runner_id,
	ROUND(AVG(EXTRACT(MINUTE FROM (r.pickup_time-c.order_time, 0)))) AS avg_arrival_time_in_mins
FROM
	runner_orders r
		JOIN customer_orders c
			ON r.order_id=c.order_id
WHERE
	r.pickup_time IS NOT NULL
GROUP BY
	r.runner_id
ORDER BY
	r.runner_id
;
````
#### Steps:
- Write a function to find the difference between the `c.order_time` and the `r.pickup_time` in minutes and the average of the time differences, rounded to the nearest whole number.
- Use a `JOIN` clause to join the `customer_orders` table with the `runner_orders` table.
- Filter the results to remove any cancelled orders and group and order the data by the `r.runner_id` to get the average arrival time per runner.

#### Answer:
| runner_id | avg_arrival_time_in_mins |
| --- | --- |
| 1 | 15 |
| 2 | 23 |
| 3 | 10 |

- Runner 1's average arrival time was 15 minutes.
- Runner 2's average arrival time was 23 minutes.
- Runner 3's average arrival time was 10 minutes.

***

**#3: Is there any relationship between the number of pizzas and how long the order takes to prepare?**

````sql
WITH order_info AS (
	SELECT
		c.order_id,
		COUNT(c.pizza_id) AS pizza_count,
		ROUND(MAX(EXTRACT(MINUTE FROM (r.pickup_time-c.order_time))),0) AS preparation_time
	FROM
		runner_orders r
			JOIN customer_orders c
				ON r.order_id=c.order_id
	WHERE
		r.pickup_time IS NOT NULL
	GROUP BY
		c.order_id
	ORDER BY
		c.order_id
)

--- end of cte ---

SELECT
	CORR(o.pizza_count, o.preparation_time) AS correlation_coefficient
FROM
	order_info o
;
````
#### Steps:
- Create a CTE finding the amount of pizzas and preparation time per order that was fulfilled.
- Use a `CORR` function to find the correlation coefficient to find the strength of the linear relationship between the two values, where -1 is a perfect negative correlation, 1 is a perfect positive correlation, and 0 is no correlation.

#### Answer:
| correlation_coefficient |
| --- |
| 0.83725... |

Since the correlation coefficient is relatively close to 1, we can say with high confidence that the higher the number of pizzas there are per order, the longer the preparation time.

***

**#4: What was the average distance travelled for each customer?**

````sql
SELECT
	c.customer_id,
	ROUND(AVG(r.distance)) AS avg_distance
FROM
	runner_orders r
		JOIN customer_orders c
			ON r.order_id=c.order_id
WHERE
	r.pickup_time IS NOT NULL
GROUP BY
	c.customer_id
ORDER BY
	c.customer_id
;
````
#### Steps:
- Run the `AVG` function on the `r.distance` column to find the average distance.
- Use a `JOIN` clause to join the `customer_orders` table to the `runner_orders` table to pull the `c.customer_id`.
- Filter out orders that were cancelled and group and order the results by the `c.customer_id`.

#### Answer:
| customer_id | avg_distance |
| 101 | 20 |
| 102 | 17 |
| 103 | 23 |
| 104 | 10 |
| 105 | 25 |

- Customer 101, 102, 104 all live 20km or less from Pizza Runner, while only Customer 103 and 105 live more than 20km away.
- This could be useful in trying to establish a radius for marketing campaigns/ads.

***

**#5: What was  the difference between the longest and shortest delivery times for all orders?**

````sql
SELECT
	MAX(r.duration)- MIN(r.duration) AS time_difference
FROM
	runner_orders r
WHERE 
	r.pickup_time IS NOT NULL
;
````
#### Steps:
- Subtract the lowest duration time from the highest duration time to find the time difference between delivery times for all orders.
- Filter out all orders that were cancelled.

#### Answer:
| time_difference |
| 30 |

The time difference between the longest and shortest delivery times is 30 minutes.

***

**#6: What was the average speed for each runner for each delivery and do you notice any trend for these values?**

````sql
WITH runner_speed AS (
	SELECT
		r.runner_id,
		r.order_id,
		MAX(r.distance/r.duration*60)::numeric AS speed
	FROM
		runner_orders r
	WHERE
		r.pickup_time IS NOT NULL
	GROUP BY
		r.runner_id,
		r.order_id
)

--- end of cte ---

SELECT
	r.runner_id,
	r.order_id,
	ROUND(AVG(speed), 1) AS avg_speed_in_kmh
FROM
	runner_speed r
GROUP BY
	r.runner_id,
	r.order_id
ORDER BY
	r.runner_id,
	r.order_id
````
#### Steps:
- Create a CTE to find the rate of speed by dividing the `r.distance` by the `r.duration` after multiplying the duration by 60 to convert the time from minutes to hours for all completed orders and cast the answer as the `numeric` data type.
- In the main query, select the `r.runner_id`, `r.order_id`, and then round the calculated average of the new `r.speed` column of the CTE.
- Group and order the results by the `r.runner_id` and `r.order_id`.

#### Answer:
| runner_id | order_id | avg_speed_in_kmh |
| --- | --- | --- |
| 1 | 1 | 37.5 |
| 1 | 2 | 44.4 |
| 1 | 3 | 40.2 |
| 1 | 10 | 60.0 |
| 2 | 4 | 35.1 |
| 2 | 7 | 60.0 |
| 2 | 8 | 93.6 |
| 3 | 5 | 40.0 |

There's not exactly a large enough result set to be able to confidently point out any trends, however from this limited result set, you can see that Runner 2 has very inconsistent delivery times, with one being an especially slow delivery time.

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
