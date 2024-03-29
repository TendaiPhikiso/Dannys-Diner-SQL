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

#### Customer Analysis: 
This information provides an overview of the spending patterns of the restaurants customers. We can easily identify our high value customers which in this case are Customer A & B who spent the most. 
Where’s customer C spent less & had the least amount of visits to the restaurant.

To enhance customer retention and increase visits, I propose offering discounts or special perks for frequent customers. Additionally, targeted marketing through personalised email campaigns can highlight exclusive promotions, creating a sense of value for the customer. Combining these strategies will not only cultivate customer loyalty but also contribute to the long-term success of the restaurant. 

<img align="center" src="https://img.freepik.com/free-vector/people-dining-asian-restaurant-men-women-eating-noodles-drink-tea_107791-4542.jpg?t=st=1709864014~exp=1709867614~hmac=2d2073a55f0a2532815fcd38a5f1cc0f4a656eace860f51a0249e66dd3afe7a5&w=1380"></a>

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


###  5. Which item was the most popular for each customer?

**SQL Query:**

```sql
WITH CTE_PopItems AS (
	SELECT
		s.customer_id AS [Customers],
		m.product_name AS [productName],
		COUNT(product_name) AS [numberOfPurchases],
		--Assigning a row number to each item for each customer based on the purchase count
		ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY COUNT(product_name) DESC) AS [item_rank]
	FROM
		sales as s INNER JOIN menu AS m ON s.product_id=m.product_id
	GROUP BY
		s.customer_id,
		m.product_name
) 
SELECT Customers,productName,numberOfPurchases
FROM CTE_PopItems
WHERE 
	item_rank = 1; 

```
#### Result Set:
| Customers | productName | numberOfPurchases |
|-----------|-------------|-------------------|
| A         | ramen       | 3                 |
| B         | sushi       | 2                 |
| C         | ramen       | 3                 |

#### Food Analysis: 
The information above provides valuable insights into customer preferences and purchasing patterns at the restaurant.  After analyzing the popularity of different menu items, it is evident that Ramen is the most in-demand dish, accounting for eight purchases. Given the widespread popularity of Ramen, it is essential to strategically enhance the menu.

To diversify the menu effectively, promoting Curry options through enticing specials and creative variations is recommended. For example, introducing a "Curry Fusion Week" could showcase unique combinations of flavors and ingredients, such as a Coconut Lemongrass Curry with a special blend of spices or a Mango Tango Curry with a tropical twist. Similarly, for Sushi, implementing a "Sushi Adventure Night" with a tasting menu that includes unconventional sushi rolls like a Spicy Tuna Mango Tango Roll or a Tempura Avocado Bliss Roll can add excitement to the menu. Offering limited-time specials with innovative ingredients and flavor profiles encourages customers to explore new tastes.
<img align="center" src="https://img.freepik.com/free-vector/traditional-japanese-food-cuisine-flat-composition-with-horizontal-view_1284-62716.jpg?t=st=1709863267~exp=1709866867~hmac=83c477ad7765cff14ab96ac5a78e257b9aa36c681fa36ccd684b35efceedffa4&w=1380"></a>


### 6. Which item was purchased first by the customer after they became a member?
**SQL Query:**

```sql
WITH CTE_membersFirstPurchase AS (
	SELECT 
		s.customer_id AS [Members],
		m.product_name AS [firstItemOrdered],
		FORMAT(s.order_date, 'D', 'en-gb') AS [orderDate],
		ROW_NUMBER() OVER(PARTITION BY s.customer_id ORDER BY MIN(order_date) ASC) AS [date_rank],
		FORMAT(mem.join_date, 'D', 'en-gb') AS [dateJoined]
		FROM 
			sales AS s  
			LEFT JOIN members AS mem ON mem.customer_id=s.customer_id
			LEFT JOIN menu AS m ON s.product_id=m.product_id
		WHERE 
			s.order_date > mem.join_date
		GROUP BY
			s.customer_id,
			mem.join_date,
			s.order_date,
			m.product_name
)
SELECT
	Members,
	dateJoined,
	orderDate,
	firstItemOrdered
FROM CTE_membersFirstPurchase
WHERE 
	date_rank = 1; 

```
#### Result Set:
| Members | dateJoined       | orderDate         | firstItemOrdered |
|---------|------------------|-------------------|------------------|
| A       | 07 January 2021  | 10 January 2021  | ramen            |
| B       | 09 January 2021  | 11 January 2021  | sushi            |

### 7. Which item was purchased just before the customer became a member? 
**SQL Query:**

```sql
WITH CTE_customerItem AS (
		SELECT 
		s.customer_id AS [Members],
		m.product_name AS [itemOrdered],
		FORMAT(s.order_date, 'D', 'en-gb') AS [orderDate],
		RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS [date_rank]
		FROM 
			sales AS s  
			LEFT JOIN members AS mem ON mem.customer_id=s.customer_id
			LEFT JOIN menu AS m ON s.product_id=m.product_id
		WHERE 
			s.order_date < mem.join_date
		GROUP BY
			s.customer_id,
			s.order_date,
			m.product_name
)
SELECT Members, orderDate, itemOrdered
FROM CTE_customerItem
WHERE 
	date_rank = 1
```
#### Result Set:
| Members | Order Date       | Item Ordered |
|---------|------------------|--------------|
| A       | 01 January 2021  | Curry        |
| A       | 01 January 2021  | Sushi        |
| B       | 04 January 2021  | Sushi        |


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

-- Filtering the records based on the condition that the order date is before the member's join date
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

###  9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

**SQL Query:**

```sql
SELECT 
	Customers,
	SUM(customerPoints) AS totalPoints -- sum customer points
FROM 
	(
		SELECT 
			s.customer_id AS [Customers],
			m.product_name,
			m.price,
			CASE
				WHEN  product_name LIKE 'sushi%' THEN (10 * m.price) * 2
				ELSE (10 * m.price)
			END AS [customerPoints]
		FROM
			sales AS s LEFT JOIN menu as m ON s.product_id=m.product_id

	) AS [customerPoint]
GROUP BY
	Customers
ORDER BY 
	totalPoints DESC
```
#### Result Set:
| Customers | totalPoints |
|-----------|-------------|
| B         | 940         |
| A         | 860         |
| C         | 360         |

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

**SQL Query:**

```sql
SELECT
	Members,
	SUM(points) AS [memberPointsTotal]
FROM 
(
	SELECT 
		s.customer_id AS [Members],
		m.product_name,
		m.price,
		s.order_date,
		(10 * m.price)* 2 AS [points]
	FROM
		sales AS s 
		LEFT JOIN menu as m ON s.product_id=m.product_id
		LEFT JOIN members AS mem ON s.customer_id=mem.customer_id
	WHERE
		s.order_date >= mem.join_date AND MONTH(order_date) != 2
) AS [memeberPoints]
GROUP BY
	Members
```
#### Result Set:
| Members | memberPointsTotal |
|---------|-------------------|
| A       | 1020              |
| B       | 440               |

### Membership Program Analysis
The data extracted from Danny's Diner members provides valuable insights into their purchasing behavior and loyalty. Currently, there are 2 members: A & B each with different total items purchased, amounts spent, and accumulated points.

To optimize and expand the existing customer loyalty program, Danny could consider the following strategies:

**Referral Program:** Implement a referral program where existing members can earn additional points or exclusive benefits for bringing in new customers. This not only promotes customer loyalty but also expands the customer base.

**Birthday Rewards:** Recognize and celebrate customers' birthdays by offering them special rewards or discounts during their birth month. This gesture adds a personal touch to the loyalty program.

**Feedback Mechanism:** Establish a feedback mechanism to understand members' preferences and gather suggestions for improvement. This will enable Danny to continuously refine the loyalty program based on customer input.

<p align="center">
<img align="center" src="https://img.freepik.com/free-vector/sushi-restaurant-banner_83728-1156.jpg?t=st=1709866074~exp=1709869674~hmac=e5176696a4fff439693efce36b83c7f957d28fcbda750e47972bdec46b8feebe&w=740">
</p>

###  Bonus | creating basic data tables that Danny and his team can use to quickly derive insights
**SQL Query:**

```sql
SELECT
	s.customer_id,
	s.order_date,
	m.product_name,
	m.price,
	CASE
		WHEN order_date >= join_date THEN 'Y'
		ELSE 'N'
	END AS [member]

FROM 
	sales AS s 
	LEFT JOIN menu AS m ON s.product_id=m.product_id
	LEFT JOIN members AS mem ON s.customer_id=mem.customer_id
ORDER BY
	customer_id,
	order_date,
	product_name

```
#### Result Set:
| customer_id | order_date | product_name | price | member |
|-------------|------------|--------------|-------|--------|
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

## Accreditation
8 week SQL Challenge by :  [Danny Ma](https://8weeksqlchallenge.com/)

