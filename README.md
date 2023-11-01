# parch-and-posey
data extraction in SQL

--1. What region has the highest total revenue ?
  
--Query:

SELECT region.name,
SUM(total_amt_usd) total_sales
FROM orders
LEFT JOIN accounts
ON accounts.id = orders.account_id
LEFT JOIN sales_reps
ON sales_reps.id= accounts.sales_rep_id
lEFT JOIN region
ON sales_reps.region_id= region.id
GROUP BY region.name
ORDER by total_sales desc

--2. Monthly trend of total orders 
 
--Query:  

SELECT DATE_TRUNC('month', o.occurred_at) AS month,
COUNT(*) AS total_orders
FROM Orders AS o
GROUP BY month
ORDER BY month

--3. What are the Average order per paper 

--Query:

SELECT round(AVG(poster_qty),2) poster,round(avg(gloss_qty),2) gloss,
round(AVG(standard_qty),2) standard
FROM orders AS o
JOIN Accounts AS a 
ON o.account_id = a.ID;

--4. Calculate bonuses for sales representatives based on the total_amt in USD for the accounts they manage. A 5% bonus for total_amt exceeding $10,000 and a 2% bonus otherwise.
 
--Query:

select sales_reps.name, sum(total_amt_usd) as total_sales, case
when sum(total_amt_usd)>10000 THEN sum(total_amt_usd) *0.05
ELSE sum(total_amt_usd)*0.02
END as bonus
FROM orders
LEFT JOIN accounts
ON accounts.id = orders.account_id
LEFT JOIN sales_reps
ON sales_reps.id= accounts.sales_rep_id
GROUP BY sales_reps.name
ORDER by bonus desc

--5. Retrieve a list of all sales representative and their region, as well as the total number of products sold by each sales representatives.

--Query:

SELECT sr.Name AS sales_rep_name, r.name AS region_name,
SUM(o.standard_qty + o.Poster_qty) AS total_products_sold
FROM Sales_reps AS sr
JOIN Region AS r ON sr.Region_id = r.Id
LEFT JOIN Accounts AS a ON sr.Id = a.Sales_rep_id
LEFT JOIN Orders AS o ON a.ID = o.account_id
GROUP BY sr.Name, r.name
ORDER BY SUM(o.standard_qty + o.Poster_qty)desc

--6. Find all customers who placed an order in the current year and the sales
representative who handled those orders.
 
SELECT DATE_PART('year', o.occurred_at) AS order_year, a.Name AS customer_name, sr.Name AS sales_rep_name
FROM Accounts AS a
JOIN Sales_reps AS sr ON a.Sales_rep_id = sr.Id
JOIN Orders AS o ON a.ID = o.account_id
WHERE DATE_PART('year', o.occurred_at)<= DATE_PART('year', CURRENT_DATE)
GROUP BY order_year, a.Name, sr.Name
ORDER BY order_year, customer_name;

--7. Which region has the highest customer acquisition cost?

 --Query:

SELECT accounts.name, COUNT(orders.id) AS num_orders
FROM accounts
JOIN orders ON accounts.id = orders.account_id
WHERE orders.occurred_at >= DATE '2016-10-01' AND orders.occurred_at <= DATE '2016-12-31'
GROUP BY accounts.name
ORDER BY num_orders DESC
LIMIT 5;
 
--8.  which marketing channels that generate the most traffic for customers who have made a purchase in the year 2016 ?
 
--Query:

SELECT web_events.channel, COUNT(web_events.id) AS num_web_events
FROM web_events
JOIN orders ON web_events.account_id = orders.account_id
WHERE orders.occurred_at >= '2016-01-01'
AND orders.occurred_at <= '2016-12-01'
GROUP BY web_events.channel
ORDER BY num_web_events DESC;

--9.The day of the week that has the highest orders

--Query:

SELECT DATE_PART('DOW', occurred_at) AS day_of_week,
SUM(total_amt_usd) AS total_order_amount,
COUNT(occurred_at) AS total_orders
FROM orders

GROUP BY DATE_PART('DOW', occurred_at)
ORDER BY total_order_amount DESC

--10. What’s the average order value for each sales rep?

 --Query:

SELECT round(AVG (total_amt_usd),2 )average_order,sales_reps.name
FROM orders 
LEFT JOIN accounts
ON accounts.id = orders.account_id 
LEFT JOIN sales_reps 
ON sales_reps.id= accounts.sales_rep_id 
GROUP BY sales_reps.name
ORDER by average_order desc 
limit 5
