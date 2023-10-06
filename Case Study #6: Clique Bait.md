# 🎣 Clique Bait - Case Study #6

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/b1385d14-1792-46e4-a83c-14fc550f1dea" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)
	- [Digital Analysis](#digital-analysis)
	- [Product Funnel Analysis](#product-funnel-analysis)
	- [Campaigns Analysis](#campaigns-analysis)
    
All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-6/) website.

***

## Objective

Danny launches Data Mart, a new online supermarket that changed to only using sustainable packaging methods from the farm to the customer as of July 2020. Danny needs help quantifying impact of this change on the sales performance of Data Mart and other business areas.

***

## Entity Relationship Diagram

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/b2c6ab38-d88b-4052-a4e0-da702f6daf2e" alt="Image" width="750">

***

## Questions and Solutions

## Digital Analysis

**#1: How many users are there?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: How many cookies does each user have on average?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: What is the unique number of visits by all users per month?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: What is the number of events for each event type?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: What is the percentage of visits that have a purchase event?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#6: What is the percentage of visits that view the checkout page but do not have a purchase event?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#7: What are the top 3 pages by number of views?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#8: What is the number of views and cart adds for each product category?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#9: What are the top 3 products by purchases?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Product Funnel Analysis

**Using a single SQL query, create a new output table that answers the following questions:**

**- How many times was each product viewed?**

**- How many times was each product added to a cart?**

**- How many times was each product added to a cart but not purchased?**

**- How many times was each product purchased?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Campaigns Analysis

**#1: Generate a table that has 1 single row for every unique `visit_id` record and has the following columns:**

- **`user_id`**
- **`visit_id`**
- **`visit_start_time`: the earliest `event_time` for each visit**
- **`page_views`: count of page views for each visit**
- **`cart_adds`: count of product cart add events for each visit**
- **`purchase`: 1/0 flag if a purchase event exists for each visit**
- **`campaign_name`: map the visit to a campaign if the `visit_start_time` falls between the `start_date` and `end_date`**
- **`impression`: count of ad impressions for each visit**
- **`click`: count of ad clicks for each visit**
- **`cart_products`: a comma separated text value with products added to the cart sorted by the order they were added to the cart**
  
**#2: Use the subsequent dataset to generate at least 5 insights for the Clique Bait team.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

**#3: Prepare a single A4 infographic that the team can use for their management reporting sessions**


***

