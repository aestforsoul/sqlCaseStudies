# Case Study #1 - Danny's Diner
1. What is the total amount each customer spent at the restaurant?
```sql
SELECT sales.customer_id, SUM(menu.price) as total_amount
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY total_amount DESC
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/4fff3a67-a064-4f93-bee4-a8108dab292e)
<br>

2. How many days has each customer visited the restaurant?
```sql
SELECT customer_id, COUNT(DISTINCT order_date) as number_of_visits
FROM sales
GROUP BY customer_id
ORDER BY number_of_visits DESC
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/e2debffa-73e9-4180-b125-be31bb4bd300)
<br>

3. What was the first item from the menu purchased by each customer?

What is the most purchased item on the menu and how many times was it purchased by all customers?
Which item was the most popular for each customer?
Which item was purchased first by the customer after they became a member?
Which item was purchased just before the customer became a member?
What is the total items and amount spent for each member before they became a member?
If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
 
