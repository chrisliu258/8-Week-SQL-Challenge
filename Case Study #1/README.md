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
***
**3. What was the first item from the menu purchased by each customer?**
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
