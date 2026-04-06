# Case Study #1: Danny's Diner 
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/main/images/week-1.png" width="300" height="300">

## Task
Danny would like to answer 10 questions about this customers purchasing patterns, sales, and top menu items at his diner. The full problem statement can be found [here](https://8weeksqlchallenge.com/case-study-1/).

## Table Schema
<img src="https://github.com/chrisliu258/8-Week-SQL-Challenge/blob/main/images/table-schema.png">

## Questions & Solutions

Queries were run on [DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138). 

**1. What is the total amount each customer spent at the restaurant?**

SQL Query:
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

SQL Query:
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

SQL Query:
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
- Customer A ordered curry and sushi; B ordered curry, C ordered 2 orders of ramen. 

***
**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

SQL Query:
 ````sql
    SELECT 
    	m.product_name,
        COUNT(m.product_name) AS totalPurchased
    FROM dannys_diner.sales AS s
    LEFT JOIN dannys_diner.menu AS m
    ON s.product_id = m.product_id
    GROUP BY m.product_name
    ORDER BY totalPurchased DESC
    LIMIT 1
````
Result:
| product_name | totalpurchased |
| ------------ | -------------- |
| ramen        | 8              |
- Ramen was the most purchased item, purchased 8 times. 

***
**5. Which item was the most popular for each customer?**

SQL Query:
````sql
    WITH customer_favorite AS(
      SELECT 
      	s.customer_id,
      	m.product_name,
      	COUNT(m.product_name) as timesOrdered,
      	RANK() OVER
      		(PARTITION BY s.customer_id
             ORDER BY COUNT(m.product_name) DESC) as itemRank
      FROM dannys_diner.sales as s
      LEFT JOIN dannys_diner.menu as m
      ON s.product_id = m.product_id
      GROUP BY s.customer_id, m.product_name
     )
    
    SELECT *
    FROM customer_favorite as cf
    WHERE cf.itemrank = 1
````
Result:
| customer_id | product_name | timesordered | itemrank |
| ----------- | ------------ | ------------ | -------- |
| A           | ramen        | 3            | 1        |
| B           | ramen        | 2            | 1        |
| B           | curry        | 2            | 1        |
| B           | sushi        | 2            | 1        |
| C           | ramen        | 3            | 1        |

- Customer A ordered ramen the most, B ordered ramen, sushi, curry an equal amount, C ordered ramen the most. 

***
**6. Which item was purchased first by the customer after they became a member?**

SQL Query:
````sql
    WITH first_order AS(
      SELECT 
      	s.customer_id,
      	s.order_date,
      	s.product_id,
      	ROW_NUMBER() OVER (
          PARTITION BY mem.customer_id
          ORDER BY s.order_date) AS row_number  	
      FROM dannys_diner.sales as s
      INNER JOIN dannys_diner.members as mem
      ON s.customer_id = mem.customer_id
      AND s.order_date > mem.join_date
     )
    
    SELECT 
    	fo.row_number,
        fo.customer_id,
        fo.order_date,
        m.product_name
    FROM first_order as fo
    LEFT JOIN dannys_diner.menu as m
    ON fo.product_id = m.product_id
    WHERE fo.row_number = 1
````

| row_number | customer_id | order_date | product_name |
| ---------- | ----------- | ---------- | ------------ |
| 1          | B           | 2021-01-11 | sushi        |
| 1          | A           | 2021-01-10 | ramen        |

- Customer A ordered ramen, customer B ordered sushi.  

***
**7. Which item was purchased just before the customer became a member?**

SQL Query
````sql
    WITH last_nonmember_order AS (
      SELECT 
        m.customer_id, 
        s.product_id,
        ROW_NUMBER() OVER (
          PARTITION BY m.customer_id
          ORDER BY s.order_date DESC) AS row_num
      FROM dannys_diner.members as m
      INNER JOIN dannys_diner.sales as s
        ON m.customer_id = s.customer_id
        AND s.order_date < m.join_date
    )
    
    SELECT 
      lno.*,
      m.product_name 
    FROM last_nonmember_order AS lno
    INNER JOIN dannys_diner.menu as m
      ON lno.product_id = m.product_id
    WHERE row_num = 1
````

Result:
| customer_id | product_id | row_num | product_name |
| ----------- | ---------- | ------- | ------------ |
| B           | 1          | 1       | sushi        |
| A           | 1          | 1       | sushi        |

- Both customer's last order before becoming members was sushi.

***
**8. What is the total items and amount spent for each member before they became a member?**

SQL Query:
````sql
    SELECT 
    	s.customer_id,
        COUNT(s.customer_id),
        SUM(m.price)
    FROM dannys_diner.sales as s
    INNER JOIN dannys_diner.members as mem
    ON s.customer_id = mem.customer_id
    AND s.order_date < mem.join_date
    LEFT JOIN dannys_diner.menu as m
    ON s.product_id =  m.product_id
    GROUP BY s.customer_id
    ORDER BY s.customer_id
````

Result:
| customer_id | count | sum |
| ----------- | ----- | --- |
| A           | 2     | 25  |
| B           | 3     | 40  |
- Before membership, Customer A ordered 2 items for $25, B ordered 3 items for $40

***
**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

SQL Query
````sql
    WITH points AS (
      SELECT 
        m.product_id, 
        CASE
          WHEN m.product_id = 1 THEN price * 20
          ELSE m.price * 10 END AS points
      FROM dannys_diner.menu as m
    )
    
    SELECT 
      s.customer_id, 
      SUM(p.points) AS points_earned
    FROM dannys_diner.sales as s
    LEFT JOIN points as p
      ON s.product_id = p.product_id
    GROUP BY s.customer_id
    ORDER BY s.customer_id;
````

Result:
| customer_id | points_earned |
| ----------- | ------------- |
| A           | 860           |
| B           | 940           |
| C           | 360           |

- Customer A earned 860 points, B 940 points, and C 360 points. 

***
**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

SQL Query:
````sql
    WITH dates AS (
      SELECT 
        m.customer_id, 
        m.join_date, 
        join_date + 6 AS double_points, 
        DATE_TRUNC(
          'month', '2021-01-31'::DATE)
          + interval '1 month' 
          - interval '1 day' AS final_date
      FROM dannys_diner.members as m
    )
    
    SELECT 
      s.customer_id, 
      SUM(CASE
        WHEN m.product_name = 'sushi' THEN 2 * 10 * m.price
        WHEN s.order_date BETWEEN d.join_date AND d.double_points THEN 2 * 10 * m.price
        ELSE 10 * m.price END) AS points
    FROM dannys_diner.sales as s
    LEFT JOIN dates AS d
      ON s.customer_id = d.customer_id
      AND d.join_date <= s.order_date
      AND s.order_date <= d.final_date
    LEFT JOIN dannys_diner.menu as m
      ON s.product_id = m.product_id
    GROUP BY s.customer_id
    ORDER BY s.customer_id
````

Result:
| customer_id | points |
| ----------- | ------ |
| A           | 1370   |
| B           | 940    |
| C           | 360    |

- Customer A earned 1370 points, B 940 points, C 360 points. 
