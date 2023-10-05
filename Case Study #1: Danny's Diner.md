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

- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.

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

- Customer A visited 4 times.
- Customer B visited 6 times.
- Customer C visited 2 times.

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

- Customer A purchased both curry and sushi for their first order.
- Customer B's first purchase was curry.
- Customer C's first purchase was ramen.

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

- Ramen is the most purchased item on the menu, being purchased 8 times.

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

#### Answer:
| customer_id | product_name | purchased_count |
| --- | --- | ---|
| A | ramen | 3 |
| B | curry | 2 |
| B | ramen | 2 |
| B | sushi | 2 |
| C | ramen | 3 |

- Customer A and C's favorite item is ramen.
- Customer B likes every menu item equally.
  
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
- Filter the results to only include orders placed after each customer's `m.join_date`.
- In the main query, join the CTE with the `menu` table to obtain the `m.product_name`and filter the results so only the highest ranking date (the earliest date after the `m.join_date`) is included.

#### Answer:
| customer_id | product_name |
| --- | --- |
| A | ramen |
| B | sushi |

- Customer A first purchased ramen after becoming a member.
- Customer B first purchased sushi after becoming a member.

***

**#7: Which item was purchased just before the customer became a member?**

````sql
WITH items_purchased_before_join_date AS (
	SELECT
		m.customer_id,
		m.join_date,
		s.order_date,
		s.product_id,
		DENSE_RANK() OVER (
			PARTITION BY m.customer_id 
				ORDER BY s.order_date DESC
		) AS rank
	FROM
		members m
		JOIN sales s
			ON m.customer_id=s.customer_id
	WHERE 
		m.join_date > s.order_date
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
	items_purchased_before_join_date i
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
- Create a CTE and utilize the `DENSE_RANK` window function to assign ranks to each customer's `s.order_date` but order the dates by `DESC` so the latest date is ranked highest.
- Filter the results to only include orders placed before each customer's `m.join_date`.
- In the main query, join the CTE with the `menu` table to obtain the `m.product_name` and filter the results so only the highest ranking date (the latest date before the `m.join_date`) is included.

#### Answer:

| customer_id | product_name |
| --- | --- |
| A | curry |
| A | sushi |
| B | sushi |

- Customer A ordered curry and sushi at the same time on the last order date before becoming a member.
- Customer B ordered sushi before becoming a member.

***

**#8: What is the total items and amount spent for each member before they became a member?**

````sql
WITH items_purchased_before_join_date AS (
	SELECT
		m.customer_id,
		m.join_date,
		s.order_date,
		s.product_id
	FROM
		members m
		JOIN sales s
			ON m.customer_id=s.customer_id
	WHERE 
		m.join_date > s.order_date
	GROUP BY
		m.customer_id,
		m.join_date,
		s.order_date,
		s.product_id
)
--- end cte ---
SELECT
	i.customer_id,
	COUNT(*) AS total_items,
	SUM(m.price) AS total_price
FROM
	items_purchased_before_join_date i
		JOIN menu m
			ON i.product_id=m.product_id

GROUP BY
	i.customer_id
ORDER BY
	i.customer_id
;

````
#### Steps:
- Create a CTE and filter the results to find what items each customer ordered before their `m.join_date`.
- In the main query, use the `COUNT` function to find the total count of items and then the `SUM` function to find the total price.
- Join the CTE with the `menu` table as the `m.price` column is only found in the `menu` table and group and order the results by `i.customer_id`.
- Note: I could also do this without using a CTE but rather than use 2 joins on information from all 3 tables, I'd rather filter the data and then use a `JOIN` on only that relevant data.

#### Answer:
| customer_id | total_items | total_price |
| --- | --- | --- |
| A | 2 | 25 |
| B | 3 | 40 |

- Customer A spent $25 on 2 items before becoming a member.
- Customer B spent $40 on 3 items before becoming a member.

***

**#9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

````sql
WITH points_calculator AS (
	SELECT
		m.product_id,
		CASE 
			WHEN m.product_name LIKE 'sushi' THEN (m.price*10)*2
			ELSE (m.price*10)
		END AS points
	FROM
		menu m
)
--- end of cte ---

SELECT
	s.customer_id,
	SUM(p.points) AS total_points
FROM
	sales s
		JOIN points_calculator p
			ON s.product_id=p.product_id
GROUP BY
	s.customer_id
ORDER BY
	s.customer_id
;

````
#### Steps:
- Create a CTE calculating how many points each menu item is worth.
- In the main query, total up the points using the `SUM` function and join the CTE with the `sales` table to pull the data from each customers' sales.
- Group and order .the results by the `s.customer_id` column.

#### Answer:
| customer_id | total_points |
| --- | --- |
| A | 860 |
| B | 940 |
| C | 360 |

- Customer A would have 860 points.
- Customer B would have 940 points.
- Customer C would have 360 points.

***

**#10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

````sql
WITH points_calculator AS (
		SELECT
			m.product_id,
			m.price*10 AS points
		FROM
			menu m
),
 --- end of cte ---

	first_week_dates AS (
		SELECT
			mbr.customer_id,
			mbr.join_date,
			CAST(
				mbr.join_date + INTERVAL '7 days' as date
				) AS last_day_of_week_one
		FROM
			members mbr
		GROUP BY
			mbr.customer_id,
			mbr.join_date

),
--- end of cte ---

 total_points AS (
		SELECT
			fw.customer_id,
			SUM(CASE 
				WHEN s.order_date BETWEEN fw.join_date AND fw.last_day_of_week_one THEN p.points*2
				WHEN s.order_date BETWEEN '2021-01-01' AND '2021-01-31' THEN p.points
				ELSE 0
			 END) AS total_points
		FROM
			first_week_dates fw
				JOIN sales s
					ON fw.customer_id=s.customer_id
				JOIN points_calculator p
					ON s.product_id=p.product_id
		GROUP BY
			fw.customer_id
		ORDER BY
			fw.customer_id
)
--- end of cte ---

SELECT
	t.customer_id,
	t.total_points
FROM
	total_points t;	 

````
#### Steps:
- Create a CTE (`points_calculator`) to calculate how many points each item is worth and another CTE (`first_week_dates`) calculating the date values for the first week after joining for each customer, making sure to use the `CAST` function after the `INTERVAL` function so both columns are the same data type.
- Create a third CTE (`total_points`) to calculate the total points for each customer, giving them the base `points` value if the `s.order_date` is within the month of January but adding a 2x points multiplier if the  `s.order_date` falls into the first week after becoming a member. We are only calculating the points for the month of January so any dates that fall outside of that range are set to 0 so they do not count.
- Select the calculated totals from the (`total_points`) CTE to get the final results. 

#### Answer:
| customer_id | total_points |
| --- | --- |
| A | 1270 |
| B | 840 |

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

