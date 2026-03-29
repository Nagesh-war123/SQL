# SQL
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100) UNIQUE,
    city VARCHAR(50),
    signup_date DATE
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2),
    stock_quantity INT
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(20),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    item_price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

INSERT INTO customers VALUES
(1, 'John', 'Smith', 'john.smith@email.com', 'Detroit', '2024-01-15'),
(2, 'Sarah', 'Johnson', 'sarah.j@email.com', 'Chicago', '2024-02-10'),
(3, 'Michael', 'Brown', 'michael.b@email.com', 'New York', '2024-03-05'),
(4, 'Emily', 'Davis', 'emily.d@email.com', 'Dallas', '2024-03-20');

INSERT INTO products VALUES
(101, 'Laptop', 'Electronics', 899.99, 20),
(102, 'Headphones', 'Electronics', 149.99, 50),
(103, 'Office Chair', 'Furniture', 199.99, 15),
(104, 'Desk Lamp', 'Furniture', 39.99, 40),
(105, 'Mouse', 'Electronics', 29.99, 100);

INSERT INTO orders VALUES
(1001, 1, '2024-04-01', 'Shipped'),
(1002, 2, '2024-04-03', 'Pending'),
(1003, 1, '2024-04-05', 'Delivered'),
(1004, 3, '2024-04-07', 'Delivered');

INSERT INTO order_items VALUES
(1, 1001, 101, 1, 899.99),
(2, 1001, 105, 2, 29.99),
(3, 1002, 103, 1, 199.99),
(4, 1003, 102, 1, 149.99),
(5, 1003, 104, 2, 39.99),
(6, 1004, 101, 1, 899.99);


Qurryes 
-- 1. Show all customers
SELECT *
FROM customers;

-- 2. Find all delivered orders
SELECT order_id, customer_id, order_date
FROM orders
WHERE status = 'Delivered';

-- 3. Total amount for each order
SELECT 
    oi.order_id,
    SUM(oi.quantity * oi.item_price) AS order_total
FROM order_items oi
GROUP BY oi.order_id
ORDER BY order_total DESC;

-- 4. Customer order count
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    COUNT(o.order_id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_orders DESC;

-- 5. Sales by product
SELECT 
    p.product_name,
    SUM(oi.quantity) AS units_sold,
    SUM(oi.quantity * oi.item_price) AS revenue
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
GROUP BY p.product_name
ORDER BY revenue DESC;

-- 6. Customers who spent more than 500
SELECT 
    c.first_name,
    c.last_name,
    SUM(oi.quantity * oi.item_price) AS total_spent
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY c.first_name, c.last_name
HAVING SUM(oi.quantity * oi.item_price) > 500
ORDER BY total_spent DESC;


*.sql linguist-language=SQL
*.sql linguist-detectable=true
