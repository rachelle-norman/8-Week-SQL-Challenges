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
- Aggregate the results by `s.customer_id` column to group the visit count for each customer.

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
						SELECT
							MIN(s.order_date)
						FROM
							sales s
			)		
)

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


***

**#4: What is the most purchased item on the menu and how many times was it purchased by all customers?**

**#5: Which item was the most popular for each customer?**

**#6: Which item was purchased first by the customer after they became a member?**

**#7: Which item was purchased just before the customer became a member?**

**#8: What is the total items and amount spent for each member before they became a member?**

**#9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

**#10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

***

## BONUS QUESTIONS

**Join All the Things**

**Recreate the table with customer_id, order_date, product_name, price, and member (Y/N).**

***

**Rank All the Things**

**Danny also requires further information about the ```ranking``` of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ```ranking``` values for the records when customers are not yet part of the loyalty program.**

