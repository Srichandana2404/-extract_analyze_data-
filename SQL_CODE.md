## Creating a database 
CREATE DATABASE ecommerce_db;
USE ecommerce_db;

## Creating tables for each .CSV files
CREATE TABLE customers (
    customer_id VARCHAR(100) PRIMARY KEY,
    customer_zip_code_prefix INT,
    customer_city VARCHAR(100),
    customer_state VARCHAR(100)
);

CREATE TABLE orderitems (
    order_id VARCHAR(100),
    product_id VARCHAR(100),
    seller_id VARCHAR(100),
    price DECIMAL(10,2),
    shipping_charges DECIMAL(10,2)
);

CREATE TABLE orders (
    order_id VARCHAR(20) PRIMARY KEY,
    customer_id VARCHAR(20),
    order_purchase_timestamp DATETIME,
    order_approved_at DATETIME
);

CREATE TABLE payments (
    order_id VARCHAR(100),
    payment_sequential INT,
    payment_type VARCHAR(50),
    payment_installments INT,
    payment_value DECIMAL(10,2)
);

CREATE TABLE products (
    product_id VARCHAR(100) PRIMARY KEY,
    product_category_name VARCHAR(100),
    product_weight_g INT,
    product_length_cm INT,
    product_height_cm INT,
    product_width_cm INT
);

## To verify whether tables are creates or not 
SELECT * FROM customers;
SELECT * FROM orderitems;
SELECT * FROM orders;
SELECT * FROM payments;
SELECT * FROM products;

## Use of SELECT, WHERE, ORDER BY, GROUP BY
SELECT customer_state, COUNT(order_id) AS total_orders
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE customer_state IN ('SP', 'RJ')
GROUP BY customer_state
ORDER BY total_orders DESC;

## Use of JOINS (INNER, LEFT, RIGHT)
## INNER JOIN
SELECT orders.order_id, customers.customer_city
FROM orders 
INNER JOIN customers  ON orders.customer_id = customers.customer_id;

## LEFT JOIN
SELECT customers.customer_id, orders.order_id
FROM customers 
LEFT JOIN orders ON customers.customer_id = orders.customer_id;

## RIGHT JOIN
SELECT orders.order_id, customers.customer_city
FROM orders 
RIGHT JOIN customers ON orders.customer_id = customers.customer_id;

## Creating Subqueires
SELECT customer_id, customer_city
FROM customers
WHERE customer_id IN (
    SELECT customer_id
    FROM orders
    WHERE MONTH(order_purchase_timestamp) = 12
);

##  Aggregate functions (SUM, AVG)
SELECT customer_state, AVG(DATEDIFF(order_approved_at, order_purchase_timestamp)) AS avg_approval_time
FROM orders 
JOIN customers ON orders.customer_id = customers.customer_id
GROUP BY customer_state;

 ## Creating views for analysis
CREATE VIEW state_order_summary AS
SELECT customer_state, COUNT(order_id) AS total_orders
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY customer_state;

select * from state_order_summary;

## Optimize queries with indexes
CREATE INDEX idx_customer_state ON customers(customer_state);
CREATE INDEX idx_order_date ON orders(order_purchase_timestamp);

## Outputs for these SQL codes are placed in ZIP file as(OUTPUT.ZIP)
