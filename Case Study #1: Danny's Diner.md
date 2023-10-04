# 🍜 Danny's Diner - Case Study #1

<img src="https://github.com/rnorman1996/8-Week-SQL-Challenges/assets/129090943/dfd83f48-d03e-462c-b132-85bf2d867c02.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)

All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-1/) website.

***

## Objective

Danny wants to use his restaurant data to understand visiting patterns, spending analysis, and menu preferences.

***

## Entity Relationship Diagram

![image](https://github.com/rnorman1996/8-Week-SQL-Challenges/assets/129090943/2f986529-489e-4b2c-919a-ccc622345392)

***

## Questions and Solutions

**#1: What is the total amount each customer spent at the restaurant?**

````sql
SELECT
	s.customer_id,
	SUM(m.price) AS total_spent
FROM
	sales s
		JOIN menu m
			ON s.product_id=m.product_id
GROUP BY
	s.customer_id
ORDER BY
	s.customer_id
;
````
#### Steps:
- Use the `JOIN` clause to merge the `sales` table and the `menu` table on the `product_id` columns in both tables since `s.customer_id` and `m.price` are from the two different tables.
- Use the `SUM` function on the `m.price` column to calculate the total amount spent per customer.
- Aggregate the results by the `s.customer_id` and order it by the same column for easier readability.

#### Answer:
| customer_id | total_spent |
| ----------- | ----------- |
| A | 76 |
| B | 74 |
| C | 36 |

***

**#2: How many days has each customer visited the restaurant?**

````sql

SELECT
	s.customer_id,
	COUNT (DISTINCT s.order_date) AS days_visited
FROM
	sales s
GROUP BY 
	s.customer_id
ORDER BY
	s.customer_id
;
````

#### Steps:
- Use the `COUNT(DISTINCT s.order_date)` function to find the unique visit count for each customer.
- Aggregate the results by the `s.customer_id` column to group the visit count for each customer.

#### Answer:
| customer_id | days_visited |
| --- | --- |
| A | 4 |
| B | 6 |
| C | 2 |

***

**#3: What was the first item from the menu purchased by each customer**

````sql

WITH first_purchase AS (
	
	SELECT
		s.customer_id,
		s.order_date AS first_order_date,
		s.product_id
	FROM
		sales s
	WHERE
		s.order_date= (
				--- subquery to find first order date ---
						SELECT
							MIN(s.order_date)
						FROM
							sales s
			) 		--- end of subquery ---	
)

--- end of cte ---

SELECT
	fp.customer_id,
	m.product_name
FROM
	first_purchase fp
		JOIN menu m
			ON fp.product_id=m.product_id
GROUP BY
	fp.customer_id,
	m.product_name
ORDER BY
	fp.customer_id

````

#### Steps:
- Create a CTE to find the first item purchased by each customer using the `s.product_id` column and using a subquery to set the `s.order_date` equal to the first date in the column.
- Use a `JOIN` clause to combine the new CTE with the `menu` table to match the `m.product_name` column to the `product_id` column in the `first_purchase` CTE.
- Use the `GROUP BY` clause to aggregate the data by `customer_id` and `product_name`.

#### Answer:

| customer_id | product_name |
| --- | ---|
| A | curry |
| A | sushi |
| B | curry |
| C | ramen |

***

**#4: What is the most purchased item on the menu and how many times was it purchased by all customers?**

````sql
SELECT 
	m.product_name,
	COUNT(s.product_id) AS purchased_count
FROM
	sales s
		JOIN menu m
			ON s.product_id=m.product_id
GROUP BY
	m.product_name
ORDER BY
	purchased_count DESC
LIMIT 1	
;
````

#### Steps:
- Use a `COUNT` function on the `s.product_id` column to find the amount of times each item was purchased.
- Use a `JOIN` clause to combine the `sales` and `menu` tables to match the `m.product_name` with the `s.product_id` in the final results.
- Order the results by descending so the item with the highest number is at the top and limit the results to 1 so only that item shows.

#### Answer:
| product_name | purchased_count |
| --- | --- |
| ramen | 8 |


***

**#5: Which item was the most popular for each customer?**

````sql

WITH most_purchased_items AS (
	SELECT
		s.customer_id,
		m.product_name,
		COUNT(s.product_id) AS purchased_count,
		DENSE_RANK() OVER (
			PARTITION BY s.customer_id
				ORDER BY COUNT(s.product_id) DESC
		) AS rank
	FROM
		sales s
			JOIN menu m
				ON s.product_id=m.product_id
	GROUP BY
		s.customer_id,
		m.product_name
)
--- end of cte ---

SELECT 
	mpi.customer_id,
	mpi.product_name,
	mpi.purchased_count
FROM
	most_purchased_items mpi
WHERE
	mpi.rank = 1
GROUP BY
	mpi.customer_id,
	mpi.product_name,
	mpi.purchased_count
ORDER BY
	mpi.customer_id
;
````
#### Steps:
- Create a CTE to find and assign a rank to the most purchased items for each customer using the `DENSE_RANK` window function in conjunction with the `PARTITION BY` clause and the `COUNT(s.product_id)` function and order it by `DESC` to ensure the highest values are ranked at 1.
- Within the CTE, join the `menu` table with the `sales` table to match the `m.product_name` with the corresponding `s.product_id`.
- In the main query, select the `customer_id`, `product_name`, and `purchased_count` columns from the CTE and filter the results so only items with the rank of 1 show in the final results.


***

**#6: Which item was purchased first by the customer after they became a member?**

````sql

WITH items_purchased_after_join_date AS (
	SELECT
		m.customer_id,
		m.join_date,
		s.order_date,
		s.product_id,
		DENSE_RANK() OVER (
			PARTITION BY m.customer_id 
				ORDER BY s.order_date
		) AS rank
	FROM
		members m
		JOIN sales s
			ON m.customer_id=s.customer_id
	WHERE 
		m.join_date < s.order_date
	GROUP BY
		m.customer_id,
		m.join_date,
		s.order_date,
		s.product_id
)
--- end of cte ---

SELECT
	i.customer_id,
	mn.product_name
FROM
	items_purchased_after_join_date i
		JOIN menu mn
			ON i.product_id=mn.product_id
WHERE
	i.rank = 1
GROUP BY
	i.customer_id,
	mn.product_name
;
````
#### Steps:
- Create a CTE and utilize the `DENSE_RANK` window function to assign ranks to each customer's `s.order_date`.
- Filter the results to only include orders placed before each customer's `m.join_date`.
- In the main query, join the CTE with the `menu` table to obtain the `m.product_name`.

#### Answer:
| customer_id | product_name |
| --- | --- |
| A | ramen |
| B | sushi |

***

**#7: Which item was purchased just before the customer became a member?**

````sql


````
#### Steps:


#### Answer:

***

**#8: What is the total items and amount spent for each member before they became a member?**

````sql


````
#### Steps:


#### Answer:

***

**#9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

````sql


````
#### Steps:


#### Answer:

***

**#10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

````sql


````
#### Steps:


#### Answer:

***

## BONUS QUESTIONS

**Join All the Things**

**Recreate the table with customer_id, order_date, product_name, price, and member (Y/N).**

````sql


````
#### Steps:


#### Answer:

***

**Rank All the Things**

**Danny also requires further information about the ```ranking``` of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ```ranking``` values for the records when customers are not yet part of the loyalty program.**

````sql


````
#### Steps:


#### Answer:

***

