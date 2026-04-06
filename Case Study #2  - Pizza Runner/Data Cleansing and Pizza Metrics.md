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

Remove null values and replace with ' ' (blank space) in a temporary table:
````sql
    CREATE TEMP TABLE temp_custorders AS
    SELECT 
      order_id, 
      customer_id, 
      pizza_id, 
      CASE
    	  WHEN exclusions IS null OR exclusions LIKE 'null' THEN ' '
    	  ELSE exclusions
    	  END AS exclusions,
      CASE
    	  WHEN extras IS NULL OR extras LIKE 'null' THEN ' ' 
    	  ELSE extras
    	  END AS extras,
    	order_time
    FROM customer_orders;

    SELECT *
    FROM temp_custorders
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

Remove null values and replace with ' ' (blank space) in a temporary table. Clean up formatting of units in distance and duration columns:
````sql
    CREATE TEMP TABLE temp_runorders AS
        SELECT 
          order_id, 
          runner_id, 
          CASE
        	  WHEN pickup_time IS null OR pickup_time LIKE 'null' THEN ' '
        	  ELSE pickup_time
              END AS pickup_time,
          CASE
        	  WHEN distance IS NULL OR distance LIKE 'null' THEN ' ' 
              WHEN distance LIKE '%km' THEN TRIM('km' from distance)
        	  ELSE distance
        	  END AS distance,
          CASE
        	  WHEN duration IS NULL OR duration LIKE 'null' THEN ' '
              WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
              WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
              WHEN duration LIKE '%minutes' THEN TRIM('minutes' from duration)
        	  ELSE duration
        	  END AS duration,
          CASE
             WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ' ' 
        	  ELSE cancellation
        	  END AS cancellation
        FROM runner_orders;

    SELECT *
        FROM temp_runorders
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
    FROM temp_custorders as co
````
Result:
| count |
| ----- |
| 14    |
- 14 orders were placed. 
***
**3. How many unique customer orders were made?**
***
**4. How many successful orders were delivered by each runner?**
***
**5. How many of each type of pizza was delivered?**
***
**6. How many Vegetarian and Meatlovers were ordered by each customer?**
***
**7. What was the maximum number of pizzas delivered in a single order?**
***
**8. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
***
**9. How many pizzas were delivered that had both exclusions and extras?**
***
**10. What was the total volume of pizzas ordered for each hour of the day?**
***
**11. What was the volume of orders for each day of the week?**
***


