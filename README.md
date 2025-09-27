1. BASIC QUERIES (SELECT, WHERE, ORDER BY, GROUP BY)

-- Select customers with shipped orders, ordered by sales (high → low)
SELECT CUSTOMERNAME, PRODUCTLINE, SALES
FROM new_schema.sales_data_sample
WHERE STATUS = 'Shipped'
ORDER BY SALES DESC;

-- Group sales by product line and sort by total sales
SELECT PRODUCTLINE, SUM(SALES) AS TotalSales
FROM new_schema.sales_data_sample
GROUP BY PRODUCTLINE
ORDER BY TotalSales DESC;

2. JOINS (INNER, LEFT, RIGHT)

-- Assume another table: new_schema.customers (CUSTOMERNAME, PHONE, ADDRESS)

-- INNER JOIN: Show orders with customer phone numbers
SELECT s.ORDERDATE, s.CUSTOMERNAME, c.PHONE, s.SALES
FROM new_schema.sales_data_sample s
INNER JOIN new_schema.customers c
ON s.CUSTOMERNAME = c.CUSTOMERNAME;

-- LEFT JOIN: Show all orders, even if customer info is missing
SELECT s.CUSTOMERNAME, s.SALES, c.ADDRESS
FROM new_schema.sales_data_sample s
LEFT JOIN new_schema.customers c
ON s.CUSTOMERNAME = c.CUSTOMERNAME;

-- RIGHT JOIN: Show all customers, even if they don’t have orders
SELECT c.CUSTOMERNAME, c.PHONE, s.SALES
FROM new_schema.sales_data_sample s
RIGHT JOIN new_schema.customers c
ON s.CUSTOMERNAME = c.CUSTOMERNAME;

3. SUBQUERIES

-- Customers whose sales are above average
SELECT CUSTOMERNAME, SALES
FROM new_schema.sales_data_sample
WHERE SALES > (SELECT AVG(SALES) FROM new_schema.sales_data_sample);

-- Top 5 highest sales orders
SELECT *
FROM new_schema.sales_data_sample s
WHERE SALES IN (
    SELECT SALES
    FROM new_schema.sales_data_sample
    ORDER BY SALES DESC
    LIMIT 5
);

4. AGGREGATE FUNCTIONS (SUM, AVG, COUNT)

-- Average sales per product line
SELECT PRODUCTLINE, AVG(SALES) AS AvgSales
FROM new_schema.sales_data_sample
GROUP BY PRODUCTLINE;

-- Total sales per year
SELECT YEAR_ID, SUM(SALES) AS TotalSales
FROM new_schema.sales_data_sample
GROUP BY YEAR_ID
ORDER BY YEAR_ID;

-- Count number of orders per status
SELECT STATUS, COUNT(*) AS TotalOrders
FROM new_schema.sales_data_sample
GROUP BY STATUS;

5. VIEWS FOR ANALYSIS

-- Create view for yearly sales summary
CREATE VIEW yearly_sales AS
SELECT YEAR_ID, SUM(SALES) AS TotalSales, AVG(SALES) AS AvgSales
FROM new_schema.sales_data_sample
GROUP BY YEAR_ID;

-- Create view for top customers
CREATE VIEW top_customers AS
SELECT CUSTOMERNAME, SUM(SALES) AS TotalSales
FROM new_schema.sales_data_sample
GROUP BY CUSTOMERNAME
ORDER BY TotalSales DESC
LIMIT 10;

6. OPTIMIZATION WITH INDEXES

-- Index on customer name
CREATE INDEX idx_customername ON new_schema.sales_data_sample(CUSTOMERNAME);

-- Index on product line
CREATE INDEX idx_productline ON new_schema.sales_data_sample(PRODUCTLINE);

-- Index on order date
CREATE INDEX idx_orderdate ON new_schema.sales_data_sample(ORDERDATE);
