<h1 align="center">Dannys Diner | SQL</h1>

<p align="center">
<img  src="https://github.com/TendaiPhikiso/Dannys-Diner-SQL/blob/main/images/img.png">
</p>

<h2 align="center">Introduction</h2>
<p align="justify">
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.
Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.
</P>

<h2 align="center">Problem Statement</h2>
<p align="justify">
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers. He plans on using these insights to help him decide whether he should expand the existing customer loyalty program.
</P>

<h2 align="center">Entity Relationship Diagram</h2>
<p align="center">
<img width=500 src="https://github.com/TendaiPhikiso/Dannys-Diner-SQL/blob/main/images/ERD-Diagram.png">
</p>

<h2 align="center">Case Study Questions</h2>

### 1. What is the total amount each customer spent at the restaurant?

**SQL Query:**

```sql
SELECT
	customer_id AS [Customers],
	FORMAT(SUM(m.price),'$#,0.00') AS [Amount Spent] --formatting the price column to represent numeric values as a currency, including a $ sign.
FROM
	dbo.sales AS s LEFT JOIN menu as m ON s.product_id=m.product_id
GROUP BY
	s.customer_id
```
#### Result Set

| Customers | Amount Spent |
|-----------|--------------|
| A         | $76.00       |
| B         | $74.00       |
| C         | $36.00       |

#### Summary: 

### 2. How many days has each customer visited the restaurant?

**SQL Query:**

```sql
SELECT
	customer_id AS [Customers],
	COUNT(order_date) AS [Days Visited]
FROM
	sales
GROUP BY
	customer_id
```
#### Result Set

| Customers | Days Visited |
|-----------|--------------|
| A         | 6            |
| B         | 6            |
| C         | 3            |

#### Summary: 

### 3. What was the first item from the menu purchased by each customer?

**SQL Query:**

```sql
SELECT
	s.customer_id AS [Customers],
	m.product_name AS [itemName],
	FORMAT(MIN(s.order_date), 'D', 'en-gb') AS [orderDate]
FROM
	sales as s INNER JOIN
	(
		SELECT customer_id, MIN(order_date) AS [firstItem_order]
		FROM sales
		GROUP BY customer_id
	) AS [firstOrder_date] ON s.customer_id=firstOrder_date.customer_id AND s.order_date =firstOrder_date.firstItem_order
	LEFT JOIN menu AS m ON s.product_id=m.product_id
GROUP BY 
	s.customer_id,
	m.product_name
ORDER BY
	s.customer_id

```
#### Result Set:

| Customers | itemName | orderDate         |
|-----------|----------|-------------------|
| A         | curry    | 01 January 2021   |
| A         | sushi    | 01 January 2021   |
| B         | curry    | 01 January 2021   |
| C         | ramen    | 01 January 2021   |

#### Summary: Customer A bought 2 items.....

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

**SQL Query:**

```sql
SELECT
	product_name AS [productName],
	COUNT(product_name) AS [numberOfPurchases]
FROM
	sales as s LEFT JOIN menu AS m ON s.product_id=m.product_id
GROUP BY
	product_name
ORDER BY
	numberOfPurchases DESC
```
#### Result Set

| productName | numberOfPurchases |
|-------------|---------------------|
| ramen       | 8                   |
| curry       | 4                   |
| sushi       | 3                   |


#### Summary: 

###  5. Which item was the most popular for each customer?

**SQL Query:**

```sql
SELECT
	customer_id,
	product_name AS [productName],
	COUNT(product_name) AS [numberOfPurchases]
FROM
	sales as s LEFT JOIN menu AS m ON s.product_id=m.product_id
GROUP BY
	product_name,
	customer_id
ORDER BY
	customer_id,
	numberOfPurchases DESC

```
#### Result Set:
| Customers | productName | numberOfPurchases |
|-----------|-------------|-------------------|
| A         | ramen       | 3                 |
| A         | curry       | 2                 |
| A         | sushi       | 1                 |
| B         | sushi       | 2                 |
| B         | curry       | 2                 |
| B         | ramen       | 2                 |
| C         | ramen       | 3                 |


#### Summary: 

### 6. Which item was purchased first by the customer after they became a member?
**SQL Query:**

```sql

```
#### Result Set:

#### Summary: 

### 7. Which item was purchased just before the customer became a member? 
**SQL Query:**

```sql

```
#### Result Set:

#### Summary: 

### 8. What is the total items and amount spent for each member before they became a member?
**SQL Query:**

```sql
SELECT
	s.customer_id AS [Members],
	COUNT(product_name) AS [totalItems],
	FORMAT(SUM(m.price),'$#,0.00') AS [Amount Spent]
FROM
	sales AS s LEFT JOIN menu as m ON s.product_id=m.product_id
	LEFT JOIN members as mem ON s.customer_id=mem.customer_id
WHERE
	s.order_date < mem.join_date
GROUP BY
	s.customer_id
```
#### Result Set:
| Members | totalItems | Amount Spent |
|---------|------------|--------------|
| A       | 2          | $25.00       |
| B       | 3          | $40.00       |

#### Summary: 

###  9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

**SQL Query:**

```sql

```
#### Result Set:

#### Summary: 

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

**SQL Query:**

```sql

```
#### Result Set:

#### Summary: 

###  Bonus Question
**SQL Query:**

```sql

```
#### Result Set:

#### Summary: 


