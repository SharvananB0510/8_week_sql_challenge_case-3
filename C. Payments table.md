The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:

- monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
- upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
- upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
once a customer churns they will no longer make payments

```sql
WITH RECURSIVE cte AS (
 SELECT customer_id, plan_id, plan_name, start_date, 
   LEAD(start_date, 1) OVER(PARTITION BY customer_id 
       ORDER BY start_date, plan_id) cutoff_date,
   price as amount
  FROM subscriptions
  JOIN plans
  USING (plan_id)
  WHERE start_date BETWEEN '2020-01-01' AND '2020-12-31'
   AND plan_name NOT IN('trial', 'churn')
),
cte1 AS(
 SELECT customer_id, plan_id, plan_name, start_date, 
   COALESCE(cutoff_date, '2020-12-3') cutoff_date, amount
 FROM cte
),
cte2 AS (
  SELECT customer_id, plan_id, plan_name, start_date, cutoff_date, 
    amount FROM cte1

  UNION ALL

  SELECT customer_id, plan_id, plan_name, 
    DATE((start_date + INTERVAL 1 MONTH)) start_date, 
    cutoff_date, amount FROM cte2
  WHERE cutoff_date > DATE((start_date + INTERVAL 1 MONTH))
   AND plan_name <> 'pro annual'
)
SELECT * FROM cte2
ORDER BY customer_id, start_date;
```

| customer_id | plan_id | plan_name     | start_date | cutoff_date | amount |
|-------------|---------|---------------|------------|-------------|--------|
| 1           | 1       | basic monthly | 2020-08-08 | 2020-12-3   | 9.90   |
| 1           | 1       | basic monthly | 2020-09-08 | 2020-12-3   | 9.90   |
| 1           | 1       | basic monthly | 2020-10-08 | 2020-12-3   | 9.90   |
| 1           | 1       | basic monthly | 2020-11-08 | 2020-12-3   | 9.90   |
| 2           | 3       | pro annual    | 2020-09-27 | 2020-12-3   | 199.00 |
| 3           | 1       | basic monthly | 2020-01-20 | 2020-12-3   | 9.90   |
| 3           | 1       | basic monthly | 2020-02-20 | 2020-12-3   | 9.90   |
| 3           | 1       | basic monthly | 2020-03-20 | 2020-12-3   | 9.90   |
|-|-|-|-|-|-|
|-|-|-|-|-|-|
|-|-|-|-|-|-|
| 625         | 2       | pro monthly   | 2020-03-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-04-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-05-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-06-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-07-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-08-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-09-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-10-28 | 2020-12-3   | 19.90  |
| 625         | 2       | pro monthly   | 2020-11-28 | 2020-12-3   | 19.90  |
| 626         | 1       | basic monthly | 2020-07-15 | 2020-12-3   | 9.90   |
|-|-|-|-|-|-|
|-|-|-|-|-|-|
|-|-|-|-|-|-|
| 1000        | 2       | pro monthly   | 2020-03-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-04-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-05-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-06-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-07-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-08-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-09-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-10-26 | 2020-12-3   | 19.90  |
| 1000        | 2       | pro monthly   | 2020-11-26 | 2020-12-3   | 19.90  |


