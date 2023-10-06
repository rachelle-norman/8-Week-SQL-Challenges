# 🥑 Foodie-Fi - Case Study #3

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/4de82a7e-81bc-4888-90ea-3afaac75ae9e" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)
	- [Customer Journey](#customer-journey)
	- [Data Analysis Questions](#data-analysis-questions)
  	- [Challenge Payment Question](#challenge-payment-question)
  	- [Outside the Box Questions](#outside-the-box-questions)

All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-3/) website.

***

## Objective

Danny launches Foodie-Fi, a new startup selling monthly and annual subscriptions to give customers unlimited on-demand access to food videos. He wants to ensure all investment decisions and new features are decided using data, focusing on subscription style data to answer his important business questions.

***

## Entity Relationship Diagram

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/1f047ad8-7c71-4ba3-93cf-6ba2f1036139" alt="Image" width="650">


***

## Questions and Solutions

## Customer Journey

**#1: Based off of the 8 sample customers provided in the sample from the `subscriptions` table, write a brief description about each customer's onboarding journey.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Data Analysis Questions

**#1: How many customers has Foodie-Fi ever had?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What is the monthly distribution of `trial` plan `start_date` values for our dataset? Use the start of the month as the `GROUP BY` value.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: What plan `start_date` values occur after the year 2020 for our dataset? Show the breakdown by count of events for each `plan_name`.**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: How many customers have churned straight after their initial free trial? What percentage is this rounded to the nearest whole number?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#6: What is the number and percentage of customer plans after their initial free trial?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#7: What is the customer count and percentage breakdown of all 5 `plan_name` values at `2020-12-31`?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#8: How many customers have upgraded to an annual plan in 2020?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#9: How many days on average does it take for a customer to switch to an annual plan from the day they join Foodie-Fi?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#10: Can you further breakdown this average value into 30 day periods? (i.e. 0-30 days, 31-60 days, etc)?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#11: How many customers downgraded from a pro monthly plan to a basic monthly plan in 2020?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Challenge Payment Question

**#1: The Foodie-Fi team wants you to create a new `payments` table for the year 2020 that includes amounts paid by each customer in the `subscriptions` table with the following requirements:**

  **- Monthly payments always occur on the same day of the month as the original `start_date` of any monthly paid plan**
  
  **- Upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately**
  
  **- Upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period**
  
  **- Once a customer churns, they will no longer make payments**
  
  **- Columns should include: `customer_id`, `plan_id`, `plan_name`, `payment_date`, `amount`, `payment_order`**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Outside the Box Questions

**#1: How would you calculate the rate of growth for Foodie-Fi?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What key metrics would you recommend Foodie-Fi management to track over time to assess performance of their overall business?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: What are some key customer journeys or experiences that you would analyze further to improve customer rentention?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: If the Foodie-Fi team were to create an exit survey shown to customers who wish to cancel their subscription, what questions would you include in the survey?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: What business levers could the Foodie-Fi team use to reduce the customer churn rate? How would you validate the effectiveness of your ideas?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***
