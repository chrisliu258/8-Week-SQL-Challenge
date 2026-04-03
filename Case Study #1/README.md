# Case Study #1: Danny's Diner 
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/main/images/week-1.png" width="300" height="300">

## Task
Danny would like to answer 10 questions about this customers purchasing patterns, sales, and top menu items at his diner. The full problem statement can be found [here](https://8weeksqlchallenge.com/case-study-1/).

## Table Schema
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/main/images/table-schema.png">

## Questions & Solutions

Queries were run on [DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138). 

**1. What is the total amount each customer spent at the restaurant?**

SQL query:
````sql
SELECT
  	s.customer_id, SUM(m.price) as totalSpent
FROM dannys_diner.sales as s
LEFT JOIN dannys_diner.menu as m
ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC; 
````
Result:
| customer_id | totalspent |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |
- Customer A spent $76, B spent $74, and C spent $36.

***
**2. How many days has each customer visited the restaurant?**

SQL query:
````sql
    SELECT
      	s.customer_id, COUNT(DISTINCT s.order_date) as daysVisited
    FROM dannys_diner.sales as s
    GROUP BY s.customer_id
    ORDER BY s.customer_id ASC;
````
Result:
| customer_id | daysvisited |
| ----------- | ----------- |
| A           | 4           |
| B           | 6           |
| C           | 2           |
- Customer A visited 4 times, B visited 6 times, and C visited 2 times.
  
***
**3. What was the first item from the menu purchased by each customer?**

SQL query:
````SQL
    WITH ranked_sales AS(
      SELECT 
      	s.customer_id,
      	s.order_date,
      	m.product_name,
      	RANK() OVER 
      		(PARTITION BY s.customer_id 
             ORDER BY s.order_date ASC) AS dateRank
      FROM dannys_diner.sales AS s
      LEFT JOIN dannys_diner.menu AS m
      ON s.product_id = m.product_id
     )
     
     SELECT
     	*
     FROM ranked_sales as rs
     WHERE rs.dateRank = 1
     ORDER BY rs.customer_id
````
Result:
| customer_id | order_date | product_name | daterank |
| ----------- | ---------- | ------------ | -------- |
| A           | 2021-01-01 | curry        | 1        |
| A           | 2021-01-01 | sushi        | 1        |
| B           | 2021-01-01 | curry        | 1        |
| C           | 2021-01-01 | ramen        | 1        |
| C           | 2021-01-01 | ramen        | 1        |
-- Customer A ordered curry and sushi; B ordered curry, C ordered 2 orders of ramen. 

***
**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
***
**5. Which item was the most popular for each customer?**
***
**6. Which item was purchased first by the customer after they became a member?**
***
**7. Which item was purchased just before the customer became a member?**
***
**8. What is the total items and amount spent for each member before they became a member?**
***
**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
***
**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
