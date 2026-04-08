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

## Ingredient Optimization

1. What are the standard ingredients for each pizza?
2. What was the most commonly added extra?
3. What was the most common exclusion?
4. Generate an order item for each record in the customers_orders table in the format of one of the following:
   - Meat Lovers
   - Meat Lovers - Exclude Beef
   - Meat Lovers - Extra Bacon
   - Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
6. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
   - For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
8. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
