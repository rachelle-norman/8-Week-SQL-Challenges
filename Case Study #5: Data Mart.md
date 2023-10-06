# 🏪 Data Mart - Case Study #5

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/320985e8-9235-4e82-8842-ccd74c370723" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Objective](#objective)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)
	- [Data Cleaning Steps](#data-cleaning-steps)
	- [Data Exploration](#data-exploration)
	- [Before and After Analysis](#before-and-after-analysis)
 	- [Bonus Questions](#bonus-questions)
    
All information for this case study has been sourced from the [8-Week SQL Challenge](https://8weeksqlchallenge.com/case-study-5/) website.

***

## Objective

Danny launches Data Mart, a new online supermarket that changed to only using sustainable packaging methods from the farm to the customer as of July 2020. Danny needs help quantifying impact of this change on the sales performance of Data Mart and other business areas.

***

## Entity Relationship Diagram

<img src="https://github.com/rachelle-norman/8-Week-SQL-Challenges/assets/129090943/0a09f366-5938-458d-b1cb-babbd55e3991" alt="Image">

***

## Questions and Solutions

## Data Cleansing Steps

**In a single query, perform the following operations and generate a new table in the `data_mart` schema named `clean_weekly_sales`**

  **- Convert the `week_date` to a `DATE` format**

  **- Add a `week_number` as the second column for each `week_date` value. Ex: January 1st to January 7th would be 1, January 8th to the 14th would be 2, etc.**

  **- Add a `month_number` with the calendar month for each `week_date` value as the 3rd column**

  **- Add a `calendar_year` column as the 4th column containing either 2018, 2019, or 2020 values**

  **- Add a new column called `age_band` after the original `segment` column using the following mapping:**
  
| segment | age_band |
| --- | --- |
| 1 | Young Adults |
| 2 | Middle Aged |
| 3 | Retirees |

**- Add a new `demographic` column using the following mapping:**

| segment | demographic |
| --- | --- |
| C | Couples |
| F | Families |

**- Ensure all `null` string values are changed to an `"unknown"` string value in the original `segment` column as well as the new `age_band` and `demographic` columns.**

**- Generate a new `avg_transaction` column rounded to 2 decimal places for each record.**


````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Data Exploration

**#1: What day of the week is used for each `week_date` value?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#2: What range of week numbers are missing from the dataset?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#3: How many total transactions were there for each year in the dataset?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#4: What is the total sales for each region for each month?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#5: What is the total count of transactions for each platform?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#6: What is the percentage of sales for Retail vs Shopify for each month?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#7: What is the percentage of sales by demographic for each year in the dataset?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#8: Which `age_band` and `demographic` values contribute the most to Retail sales?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

**#9: Can we use the `avg_transaction` column to find the average transaction size for each year for Retail vs Shopify? If not, how would you calculate it instead?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Before and After Analysis

**The `week_date` value of `2020-06-15` is the baseline week as this is when the sustainable packaging changes came into effect. All `week_date` values for `2020-06-15` mark the start of the period AFTER the change and all of the previous `week_date` values mark BEFORE the change.**

**#1: What is the total sales for the 4 weeks before and after `2020-06-15`? What is the growth or reduction rate in actual values and percentages of sales?**

**#2: What about the entire 12 weeks before and after?**

**#3: How do the sales metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***

## Bonus Questions

**#1. Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?**

  **- `region`**
  **- `platform`**
  **- `age_band`**
  **- `demographic`**
  **- `customer_type`**
  
**#2: Do you have any further recommendations for the Data Mart team or any interesting insights based off this analysis?**

````sql
SQL goes here.
````
#### Steps:
- Steps go here

#### Answer:
Answer goes here

***
