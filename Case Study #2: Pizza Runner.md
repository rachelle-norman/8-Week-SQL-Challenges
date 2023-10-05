# 🍕 Pizza Runner - Case Study #2

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/ec8ed386-9efb-4eed-baaa-ee44bc7546ae" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)

All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-2/) website.

***

## Objective

Danny wants to use his restaurant data to understand visiting patterns, spending analysis, and menu preferences.

***

## Entity Relationship Diagram

![image](https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/5f275415-1782-47bc-b289-e46b68184a59)


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
