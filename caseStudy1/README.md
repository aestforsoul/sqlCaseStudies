# Case Study #1 - Danny's Diner
1. What is the total amount each customer spent at the restaurant?
```sql
SELECT sales.customer_id, SUM(menu.price) AS total_amount
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
SELECT customer_id, COUNT(DISTINCT order_date) AS number_of_visits
FROM sales
GROUP BY customer_id
ORDER BY number_of_visits DESC
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/e2debffa-73e9-4180-b125-be31bb4bd300)
<br>

3. What was the first item from the menu purchased by each customer?
```sql
SELECT DISTINCT sales.customer_id, menu.product_name
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id
WHERE (sales.customer_id, sales.order_date) IN (
SELECT customer_id, MIN(order_date)
FROM sales
GROUP BY customer_id)
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/1ec071e7-6223-4ffd-a671-cec6b66981cd)
<br>

4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
SELECT menu.product_name, COUNT(sales.product_id) as product_num
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY product_num DESC
LIMIT 1
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/6f597cc9-cb20-4329-8f4e-aa8a1bca19ac)
<br>

5. Which item was the most popular for each customer?
```sql
WITH orders AS (
SELECT sales.customer_id, menu.product_name, COUNT(sales.product_id) AS product_num
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, menu.product_name)
SELECT customer_id, product_name
FROM orders
WHERE (customer_id, product_num) IN (
SELECT customer_id, MAX(product_num)
FROM orders
GROUP BY customer_id)
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/ddbcfb8d-6565-4e2c-afcf-e0fe17efab87)
<br>

6. Which item was purchased first by the customer after they became a member?
```sql
WITH orders AS (
SELECT sales.customer_id, sales.product_id, sales.order_date
FROM sales
LEFT JOIN members
ON sales.customer_id = members.customer_id
WHERE sales.order_date >= members.join_date)
SELECT customer_id, product_name
FROM orders
LEFT JOIN menu
ON orders.product_id = menu.product_id
WHERE (customer_id, order_date) IN (
SELECT customer_id, MIN(order_date)
FROM orders
GROUP BY customer_id)
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/9e64a463-37dc-4167-af42-fd856e450e3f)
<br>

7. Which item was purchased just before the customer became a member?
```sql
WITH orders AS (
SELECT sales.customer_id, sales.product_id, sales.order_date
FROM sales
LEFT JOIN members
ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date)
SELECT customer_id, product_name
FROM orders
LEFT JOIN menu
ON orders.product_id = menu.product_id
WHERE (customer_id, order_date) IN (
SELECT customer_id, MAX(order_date)
FROM orders
GROUP BY customer_id)
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/b9a8ccca-ae63-49ce-9b4f-431c437eea97)
<br>

8. What is the total items and amount spent for each member before they became a member?
```sql
WITH orders AS (
SELECT sales.customer_id, sales.product_id, sales.order_date
FROM sales
LEFT JOIN members
ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date)
SELECT customer_id, COUNT(orders.product_id) AS total_items, SUM(menu.price) AS amount
FROM orders
LEFT JOIN menu
ON orders.product_id = menu.product_id
GROUP BY orders.customer_id
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/68d163ef-e3b9-472b-af32-86664e48d1a7)
<br>

9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
WITH orders AS(
SELECT sales.customer_id, 
CASE WHEN menu.product_name = 'sushi' THEN menu.price * 20
ELSE menu.price * 10 
END AS points
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id)
SELECT customer_id, SUM(points)
FROM orders
GROUP BY customer_id
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/fdd211bb-64cf-40e5-911a-e6a61f6c6beb)
<br>

10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql
WITH orders AS(
SELECT sales.customer_id, sales.order_date,
CASE WHEN ((sales.order_date >= members.join_date) AND (sales.order_date < members.join_date + integer '7')) THEN menu.price * 20
	WHEN (menu.product_name = 'sushi') THEN menu.price * 20
	ELSE menu.price * 10 
END AS points
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id
INNER JOIN members
ON sales.customer_id = members.customer_id)
SELECT customer_id, SUM(points)
FROM orders
WHERE order_date < '2021-02-01'
GROUP BY customer_id
```
Result:<br>
![image](https://github.com/aestforsoul/sqlCaseStudies/assets/80065994/97fb0dd5-aaef-4559-9984-7b5dcd662319)
<br>
