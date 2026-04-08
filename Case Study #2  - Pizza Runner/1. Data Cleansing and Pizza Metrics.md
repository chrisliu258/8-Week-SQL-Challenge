# Case Study #2: Pizza Runner
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/9aa152eae1c8429efa6e7e74f512a7e42396ab94/images/week-2.png" height=300 width=300>

## Task
Danny started a pizza business, hiring runners to deliver his pizzas to custoemrs, and would like to answer questions regarding: 
- Pizza Metrics
- Runner and Customer Experience
- Ingredient Optimisation
- Pricing and Ratings.
Additionally, Danny requires some assistance cleaning his datasets. 

The full problem statement can be found [here](https://8weeksqlchallenge.com/case-study-2/). Due to length/number of questions, this case study is divided into 4 files. 

## Table Schema
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/9aa152eae1c8429efa6e7e74f512a7e42396ab94/images/week2-table-schema.png">

## Data Cleansing

Table 1 - Runners: Requires no cleansing
| runner_id | registration_date |
| --------- | ----------------- |
| 1         | 2021-01-01        |
| 2         | 2021-01-03        |
| 3         | 2021-01-08        |
| 4         | 2021-01-15        |

Table 2 - customer_orders: There are blank spaces and null values in the exclusions and extras columns. 
| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
| -------- | ----------- | -------- | ---------- | ------ | ------------------- |
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        | null       | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        | null       | null   | 2020-01-08 21:03:13 |
| 7        | 105         | 2        | null       | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        | null       | null   | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        | null       | null   | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

Remove null values and replace with ' ' (blank space) with UPDATE statement:
````sql
UPDATE customer_orders
SET exclusions = CASE 
    WHEN TRIM(exclusions) = '' THEN NULL
    WHEN exclusions = 'null' THEN NULL
    ELSE exclusions
	END,
	extras = CASE 
    WHEN TRIM(extras) = '' THEN NULL
    WHEN extras = 'null' THEN NULL
    ELSE extras
    END;

SELECT *
FROM customer_orders
````
Result:
| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
| -------- | ----------- | -------- | ---------- | ------ | ------------------- |
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        |            | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        |            |        | 2020-01-08 21:03:13 |
| 7        | 105         | 2        |            | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        |            |        | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        |            |        | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

Table 3 - runner_orders: Again, there are null values and blank spaces found in multiple columns. The distance and duration columns have inconsistent formatting. 
| order_id | runner_id | pickup_time         | distance | duration   | cancellation            |
| -------- | --------- | ------------------- | -------- | ---------- | ----------------------- |
| 1        | 1         | 2020-01-01 18:15:34 | 20km     | 32 minutes |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20km     | 27 minutes |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4km   | 20 mins    |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40         |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15         |                         |
| 6        | 3         | null                | null     | null       | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25km     | 25mins     | null                    |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4 km  | 15 minute  | null                    |
| 9        | 2         | null                | null     | null       | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10km     | 10minutes  | null                    |

Remove null values and replace with ' ' (blank space) using and UPDATE statement. Clean up formatting of units in distance and duration columns:
````sql
    UPDATE runner_orders
    SET pickup_time = CASE 
        WHEN TRIM(pickup_time) = '' THEN NULL
        WHEN pickup_time = 'null' THEN NULL
        ELSE pickup_time
	    END,
	    distance = CASE 
        WHEN TRIM(distance) = '' THEN NULL 
        WHEN distance = 'null' THEN NULL
        WHEN distance LIKE '%km' THEN TRIM('km' from distance)
        ELSE distance
        END,
        duration = CASE
        WHEN TRIM(duration) = '' THEN NULL 
        WHEN duration = 'null' THEN NULL
        WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
        WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
        WHEN duration LIKE '%minutes' THEN TRIM('minutes' from duration)
        ELSE duration
        END,
        cancellation = CASE 
        WHEN TRIM(cancellation) = '' THEN NULL 
        WHEN cancellation = 'null' THEN NULL
        ELSE cancellation
        END;

SELECT *
FROM runner_orders
````

| order_id | runner_id | pickup_time         | distance | duration | cancellation            |
| -------- | --------- | ------------------- | -------- | -------- | ----------------------- |
| 1        | 1         | 2020-01-01 18:15:34 | 20       | 32       |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20       | 27       |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4     | 20       |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40       |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15       |                         |
| 6        | 3         |                     |          |          | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25       | 25       |                         |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4     | 15       |                         |
| 9        | 2         |                     |          |          | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10       | 10       |                         |


Table 4, 5, 6: pizza_names, pizza_recipes, pizza_toppings require no cleaning. 

| pizza_id | pizza_name |
| -------- | ---------- |
| 1        | Meatlovers |
| 2        | Vegetarian |

| pizza_id | toppings                |
| -------- | ----------------------- |
| 1        | 1, 2, 3, 4, 5, 6, 8, 10 |
| 2        | 4, 6, 7, 9, 11, 12      |

| topping_id | topping_name |
| ---------- | ------------ |
| 1          | Bacon        |
| 2          | BBQ Sauce    |
| 3          | Beef         |
| 4          | Cheese       |
| 5          | Chicken      |
| 6          | Mushrooms    |
| 7          | Onions       |
| 8          | Pepperoni    |
| 9          | Peppers      |
| 10         | Salami       |
| 11         | Tomatoes     |
| 12         | Tomato Sauce |

The data is now cleansed and ready for analysis. 

## Pizza Metrics

**1. How many pizzas were ordered?**
SQL Query:
````sql
    SELECT COUNT(co.order_id)
    FROM customer_orders as co
````
Result:
| count |
| ----- |
| 14    |
- 14 pizzas were ordered. 
***
**3. How many unique customer orders were made?**

SQL Query:
````sql
SELECT COUNT(DISTINCT co.order_id) as unique_orders
FROM customer_orders as co
````
Result:
| unique_orders |
| ------------- |
| 10            |

- 10 orders were placed. 

***
**4. How many successful orders were delivered by each runner?**

SQL Query:
````sql
    SELECT ro.runner_id,
    	COUNT(ro.order_id)
    FROM runner_orders as ro
    WHERE ro.cancellation IS NULL
    GROUP BY ro.runner_id
````
Result:
| runner_id | count |
| --------- | ----- |
| 1         | 4     |
| 2         | 3     |
| 3         | 1     |

- Runner 1 delivered 4 orders, 2 delivered 3, 3 delivered 1. 

***
**5. How many of each type of pizza was delivered?**

SQL Query:
````sql
    SELECT
    	pn.pizza_name,
        COUNT(pizza_name)
    FROM customer_orders as co
    INNER JOIN runner_orders as ro
    ON co.order_id = ro.order_id
    LEFT JOIN pizza_names pn
    ON co.pizza_id = pn.pizza_id
    WHERE ro.distance IS NOT NULL
    GROUP BY pn.pizza_name
````
Result:
| pizza_name | count |
| ---------- | ----- |
| Meatlovers | 9     |
| Vegetarian | 3     |

- 9 Meatlovers and 3 Vegetarian pizzas where delivered. 

***
**6. How many Vegetarian and Meatlovers were ordered by each customer?**

SQL Query:
````sql
    SELECT
    	co.customer_id,
    	pn.pizza_name,
        COUNT(pizza_name) as num_ordered
    FROM customer_orders as co
    LEFT JOIN pizza_names pn
    ON co.pizza_id = pn.pizza_id
    GROUP BY co.customer_id, pn.pizza_name
    ORDER BY co.customer_id ASC, num_ordered DESC
````
Result:
| customer_id | pizza_name | num_ordered |
| ----------- | ---------- | ----------- |
| 101         | Meatlovers | 2           |
| 101         | Vegetarian | 1           |
| 102         | Meatlovers | 2           |
| 102         | Vegetarian | 1           |
| 103         | Meatlovers | 3           |
| 103         | Vegetarian | 1           |
| 104         | Meatlovers | 3           |
| 105         | Vegetarian | 1           |
- Customer 101 ordered 2 meatlovers and 1 vegetarian; 102 ordered 2 meatlovers and 1 vegetarian; 103 ordered 3 meatlovers and 1 vegetarian; 104 ordered 3 meatlovers; 105 ordered 1 vegetarian. 
***
**7. What was the maximum number of pizzas delivered in a single order?**

SQL Query:
````sql
    SELECT
    	ro.order_id,
        COUNT(ro.order_id) as order_size
    FROM customer_orders as co
    INNER JOIN runner_orders as ro
    ON co.order_id = ro.order_id
    WHERE ro.cancellation IS NULL
    GROUP BY ro.order_id
    ORDER BY ro.order_id ASC
````
Result:
| order_id | order_size |
| -------- | ---------- |
| 1        | 1          |
| 2        | 1          |
| 3        | 2          |
| 4        | 3          |
| 5        | 1          |
| 7        | 1          |
| 8        | 1          |
| 10       | 2          |
- The largest order was for 3 pizzas.

***
**8. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

SQL Query:
````sql
    SELECT
    	co.customer_id,
        COUNT(CASE 
              	WHEN co.exclusions IS NOT NULL OR co.extras IS NOT NULL
              	THEN 1
              END) AS changed_order,
        COUNT(CASE
              	WHEN co.exclusions IS NULL AND co.extras IS NULL
              	THEN 1
              END) AS no_changes
    FROM customer_orders as co
    INNER JOIN runner_orders as ro
    ON ro.order_id = co.order_id
    WHERE ro.cancellation IS NULL
    GROUP BY co.customer_id
    ORDER BY co.customer_id ASC;
````
Result:
| customer_id | changed_order | no_changes |
| ----------- | ------------- | ---------- |
| 101         | 0             | 2          |
| 102         | 0             | 3          |
| 103         | 3             | 0          |
| 104         | 2             | 1          |
| 105         | 1             | 0          |

- Customer 101 received 2 pizzas and made no changes; Customer 102 received 3 pizzas and made no changes; Customer 103 received 3 pizzas and modified all 3; Customer 104 received 3 pizzas and modified 1; Customer 105 received 1 pizza and had it modified.
  
***
**9. How many pizzas were delivered that had both exclusions and extras?**

SQL Query:
````sql
    SELECT
        COUNT(CASE 
              	WHEN co.exclusions IS NOT NULL AND co.extras IS NOT NULL
              	THEN 1
              END) AS changed_order
    FROM customer_orders as co
    INNER JOIN runner_orders as ro
    ON ro.order_id = co.order_id
    WHERE ro.cancellation IS NULL
````
Result:
| changed_order |
| ------------- |
| 1             |
- There was a single order delivered where a customer asked for both excluded and extra toppings. 

***
**10. What was the total volume of pizzas ordered for each hour of the day?**

SQL Query:
````sql
    SELECT 
    	EXTRACT(HOUR FROM co.order_time) AS hour_ordered, 
        COUNT(co.order_id) AS num_ordered
    FROM customer_orders as co
    GROUP BY EXTRACT(HOUR FROM co.order_time)
    ORDER by hour_ordered ASC
````
Result: 
| hour_ordered | num_ordered |
| ------------ | ----------- |
| 11           | 1           |
| 13           | 3           |
| 18           | 3           |
| 19           | 1           |
| 21           | 3           |
| 23           | 3           |
 - 1 pizza was ordered between 11AM and 12PM, 3 pizzas between 1PM and 2PM, 3 pizzas between 6PM and 7PM, 1 pizza between 7PM and 8PM, 3 pizzas between 9PM and 10PM, 3 pizzas between 11PM and 12AM. 

***
**11. What was the volume of orders for each day of the week?**

SQL Query:
````sql
    SELECT 
    	to_char(co.order_time, 'Day') AS day_ordered, 
        COUNT(co.order_id) AS num_ordered
    FROM customer_orders as co
    GROUP BY to_char(co.order_time, 'Day')
````
Result:
| day_ordered | num_ordered |
| ----------- | ----------- |
| Saturday    | 5           |
| Thursday    | 3           |
| Friday      | 1           |
| Wednesday   | 5           |

- 5 pizzas were ordered Wednesday, 3 on Thursday, 1 on Friday, and 5 on Saturday. 
***


