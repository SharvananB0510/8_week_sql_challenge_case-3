## 1. How many customers has Foodie-Fi ever had?

### SOLUTION
``` sql
SELECT 
    COUNT(DISTINCT customer_id) AS Customer_count
FROM
    subscriptions;
```
|Customer_count|
|---------------
1000|

## 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value

### SOLUTION
``` sql
SELECT 
    MONTH(S.start_date) AS Month, COUNT(DISTINCT S.customer_id) as Customers
FROM
    subscriptions S
        JOIN
    plans P USING (plan_id)
WHERE
    P.plan_name = 'trial'
GROUP BY month;
```
| Month | Customers |
|-------|-----------|
| 1     | 88        |
| 2     | 68        |
| 3     | 94        |
| 4     | 81        |
| 5     | 88        |
| 6     | 79        |
| 7     | 89        |
| 8     | 88        |
| 9     | 87        |
| 10    | 79        |
| 11    | 75        |
| 12    | 84        |

## 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name

``` sql
SELECT 
    YEAR(s.start_date) AS Events,
    p.Plan_name,
    COUNT(S.plan_id) AS Plan_count
FROM subscriptions S
JOIN plans P ON s.plan_id = p.plan_id
WHERE YEAR(s.start_date) > 2020
GROUP BY events , p.plan_name;
```
| Events | Plan_name      | Plan_count |
|--------|----------------|------------|
| 2021   | churn          | 71         |
| 2021   | pro monthly    | 60         |
| 2021   | pro annual     | 63         |
| 2021   | basic monthly  | 8          |

## 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

``` sql
SELECT 
    COUNT(DISTINCT CASE
            WHEN P.plan_name = 'churn' THEN S.customer_id
        END) AS Churn_count,
    COUNT(DISTINCT S.customer_id) AS Total_count,
    ROUND((COUNT(DISTINCT CASE
                    WHEN P.plan_name = 'churn' THEN S.customer_id
                END) / COUNT(DISTINCT S.customer_id)) * 100,
            1) AS Churn_rate
FROM
    subscriptions S
        JOIN
    plans P USING (plan_id);
```
| Churn_count | Total_count | Churn_rate |
|-------------|-------------|------------|
| 307         | 1000        | 30.7       |

## 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

``` sql
with cte as(
Select S.customer_id,S.start_date,P.plan_name,
lead(P.plan_name) over (partition by customer_id order by plan_id) le
from subscriptions S
join plans P using(plan_id)
)
select count(*) as Trail_churn_count
	,Round(count(*)/(select count(distinct customer_id) from subscriptions),0) *100 as percentage from 
    cte C
where plan_name='trial' and le = 'churn';
```
Trail_churn_count| Percentage|
-----------------|-----------|
92               |9

## 6. What is the number and percentage of customer plans after their initial free trial?

``` sql
WITH cte_next_plan AS
(
	SELECT *,
	       LEAD(plan_id, 1) OVER (PARTITION BY customer_id ORDER BY plan_id) AS next_plan
	FROM subscriptions
),

planning AS --create number and percentage
(
    SELECT c.next_plan,
           COUNT(DISTINCT customer_id) AS customer_count,
           (100 * CAST(COUNT(DISTINCT customer_id) AS FLOAT) / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions)) AS percentage
    FROM cte_next_plan c
	LEFT JOIN plans p 
	ON p.plan_id = c.next_plan
	WHERE c.plan_id = 0 
		AND c.next_plan is not null
	GROUP BY c.next_plan
	)

SELECT p.plan_name, 
	   s.customer_count, 
	   s.percentage
FROM planning s
LEFT JOIN plans p 
ON p.plan_id = s.next_plan;
```

plan_name | customer_count | percentage
-- | -- | --
basic monthly | 546 | 54.6
pro monthly | 325 | 32.5
pro annual | 37 | 3.7
churn | 92 | 9.2

## 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

``` sql
WITH plansDate AS (
  SELECT 
    s.customer_id,
    s.start_date,
	p.plan_id,
    p.plan_name,
    LEAD(s.start_date) OVER(PARTITION BY s.customer_id ORDER BY s.start_date) AS next_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT 
  plan_id,
  plan_name,
  COUNT(*) AS customers,
  CAST(100*COUNT(*) AS FLOAT) 
      / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) AS conversion_rate
FROM plansDate
WHERE (next_date IS NOT NULL AND (start_date < '2020-12-31' AND next_date > '2020-12-31'))
  OR (next_date IS NULL AND start_date < '2020-12-31')
GROUP BY plan_id, plan_name
ORDER BY plan_id;
```
plan_name | total | percentage
-- | -- | --
trial | 19 | 1.9
basic monthly | 224 | 22.4
pro monthly | 326 | 32.6
pro annual | 195 | 19.5
churn | 235 | 23.5

## 8. How many customers have upgraded to an annual plan in 2020?

``` sql
SELECT 
  COUNT(DISTINCT customer_id) AS customer_count
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE p.plan_name = 'pro annual'
  AND YEAR(s.start_date) = 2020;
```
Customer_count|
--------------
195

## 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

``` sql
WITH trialPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS trial_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'trial'
),
annualPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS annual_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'pro annual'
)

SELECT 
  ROUND(AVG(CAST(DATEDIFF(annual_date,trial_date) AS FLOAT)),0) AS Avg_days_to_annual
FROM trialPlan t
JOIN annualPlan a 
ON t.customer_id = a.customer_id;
```

Avg_days_to_annual|
------------------|
104|


## 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)

```sql
WITH trial_plan AS (
    SELECT customer_id, 
	   start_date AS trial_date
    FROM subscriptions
    WHERE plan_id = 0
),
annual_plan AS (
    SELECT customer_id,
	   start_date as annual_date
    FROM subscriptions
    WHERE plan_id = 3
)

SELECT
    CONCAT(FLOOR(DATEDIFF(trial_date, annual_date) / 30) * 30, '-', FLOOR(DATEDIFF(trial_date, annual_date) / 30) * 30 + 30, ' days') AS period,
    COUNT(*) AS total_customers,
    ROUND(AVG(DATEDIFF(trial_date, annual_date)), 0) AS avg_days_to_upgrade
FROM trial_plan tp
JOIN annual_plan ap ON tp.customer_id = ap.customer_id
WHERE ap.annual_date IS NOT NULL
GROUP BY FLOOR(DATEDIFF(trial_date, annual_date) / 30);
```

period | total_customers | avg_days_to_upgrade
-- | -- | --
0 - 30 days  | 48 | 9
30 - 60 days  | 25 | 41
60 - 90 days  | 33 | 70
90 - 120 days  | 35  | 99
120 - 150 days  | 43  | 133
150 - 180 days  | 35  | 161
180 - 210 days  | 27  | 190
210 - 240 days  | 4  | 240
240 - 270 days  | 5  | 257
270 - 300 days  | 1  | 285
300 - 330 days  | 1  | 327
330 - 360 days  | 1  | 346

## 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020? 


 ``` sql 
 WITH nextPlan AS (
  SELECT 
    s.customer_id,
    s.start_date,
    p.plan_name,
    LEAD(p.plan_name) OVER(PARTITION BY s.customer_id ORDER BY p.plan_id) AS next_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT COUNT(*) AS Pro_to_basic_monthly
FROM nextPlan
WHERE plan_name = 'pro monthly'
  AND next_plan = 'basic monthly'
  AND YEAR(start_date) = 2020;
```
|Pro_to_basic_monthly|
-----------------|
|0|




