## A. Customer Journey

Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

Solution:

 ``` sql
With subs as(
select S.customer_id
		,P.plan_name
        ,S.start_date
  from Plans P join subscriptions S using(plan_id)
  )
  
  select * from subs 
  where customer_id IN (1,10,25,150,300,500,750,1000);
 ```

customer_id | plan_name | start_date|
------------|------------|----------|
1	|trial	|2020-08-01
1	|basic monthly|	2020-08-08
10|	trial	|2020-09-19
10|	pro monthly	|2020-09-26
25|	trial	|2020-05-10
25|	basic monthly	|2020-05-17
25|	pro monthly	|2020-06-16
150|	trial	|2020-02-05
150	|pro monthly|	2020-02-12
300	|trial	|2020-04-06
300	|basic monthly	|2020-04-13
300	|pro annual	|2020-10-04
500	|trial	|2020-09-16
500	|basic monthly	|2020-09-23
500	|pro annual	|2020-12-21
750	|trial	|2020-07-03
750	|churn	|2020-07-10
1000	|trial	|2020-03-19
1000	|pro monthly	|2020-03-26
1000	|churn	|2020-06-04



### Brief description of the customers' journey based on the provided data:

- **Customer 1** begins with a trial plan on 2020-08-01 and upgrades to a basic monthly plan on 2020-08-08.

- **Customer 10** starts with a trial plan on 2020-09-19 and later switches to a pro monthly plan on 2020-09-26.

- **Customer 25** starts with a trial plan on 2020-05-10, upgrades to a basic monthly plan on 2020-05-17, and then to a pro monthly plan on 2020-06-16.

- **Customer 150** begins with a trial plan on 2020-02-05 and upgrades to a pro monthly plan on 2020-02-12.

- **Customer 300** starts with a trial plan on 2020-04-06, switches to a basic monthly plan on 2020-04-13, and then to a pro annual plan on 2020-10-04.

- **Customer 500** begins with a trial plan on 2020-09-16, upgrades to a basic monthly plan on 2020-09-23, and eventually to a pro annual plan on 2020-12-21.

- **Customer 750** starts with a trial plan on 2020-07-03 and churns on 2020-07-10.

- **Customer 1000** begins with a trial plan on 2020-03-19, switches to a pro monthly plan on 2020-03-26, and churns on 2020-06-04.
