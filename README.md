# Case Study #3 - Foodie-Fi

## Introduction

- Danny launched Foodie-Fi in 2020, a streaming service exclusively for food-related content, like Netflix but for cooking shows. Customers could buy monthly or annual subscriptions for unlimited access to exclusive food videos.

- Danny built Foodie-Fi with a focus on data-driven decisions. He wanted to use digital subscription data to make smart investment choices and develop new features for the platform.

## Challange

- A brief description of the onboarding journey for each of the 8 sample customers provided in the subscriptions table.
- Analyze various aspects of Foodie-Fi's subscription data, including customer count, plan distribution, churn rate, and upgrade trends.
- Creation of a new payments table for the year 2020 including amounts paid by each customer, following specific requirements.

## Data set

### 1. Plans table
Customers can choose which plans to join Foodie-Fi when they first sign up.

There are 5 customer plans:

- Basic plan - customers have limited access and can only stream their videos and is only available monthly at $9.90
- Pro plan - customers have no watch time limits and are able to download videos for offline viewing. Pro plans start at $19.90 a month or $199 for an annual subscription.
- Trial plan - Customers can sign up to an initial 7 day free trial will automatically continue with the pro monthly subscription plan unless they cancel, downgrade to basic or upgrade to an annual pro plan at any point during the trial.
- Churn plan - When customers cancel their Foodie-Fi service - they will have a churn plan record with a null price but their plan will continue until the end of the billing period.

### 2. Subscriptions table

Customer subscriptions show the exact date where their specific plan_id starts.

- If customers downgrade from a pro plan or cancel their subscription - the higher plan will remain in place until the period is over - the start_date in the subscriptions table will reflect the date that the actual plan changes.
- When customers upgrade their account from a basic plan to a pro or annual pro plan - the higher plan will take effect straightaway.
- When customers churn - they will keep their access until the end of their current billing period but the start_date will be technically the day they decided to cancel their service.

## Tables

- Plans
- Subscriptions


### ER - Diagram

![image](https://github.com/SharvananB0510/8_week_sql_challenge_case-3/assets/69303949/2408854c-e5d6-4340-8209-ead4b5428074)

## Questions

### A. Customer Journey

Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

### B. Data Analysis Questions

1. How many customers has Foodie-Fi ever had?
2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
6. What is the number and percentage of customer plans after their initial free trial?
7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
8. How many customers have upgraded to an annual plan in 2020?
9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

### C. Challenge Payment Question

The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:

- monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
- upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
- upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
- once a customer churns they will no longer make payments

### D. Outside The Box Questions

1. What key metrics would you recommend Foodie-Fi management to track over time to assess performance of their overall business?
2. What are some key customer journeys or experiences that you would analyse further to improve customer retention?
3. If the Foodie-Fi team were to create an exit survey shown to customers who wish to cancel their subscription, what questions would you include in the survey?
4. What business levers could the Foodie-Fi team use to reduce the customer churn rate? How would you validate the effectiveness of your ideas?
