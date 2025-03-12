# ecommerce


-- Create database and user
CREATE DATABASE IF NOT EXISTS ecommerce;
CREATE USER IF NOT EXISTS 'ecommerce'@'localhost' IDENTIFIED BY 'ecommerce123';
GRANT ALL PRIVILEGES ON ecommerce.* TO 'ecommerce'@'localhost';
FLUSH PRIVILEGES;

USE ecommerce;


-- Users table
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('CUSTOMER', 'ADMIN'))
);

-- Products table
CREATE TABLE products (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DOUBLE NOT NULL,
    category VARCHAR(255) NOT NULL
);

-- Inventory table
CREATE TABLE inventory (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- Carts table
CREATE TABLE carts (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Cart items table
CREATE TABLE cart_items (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    cart_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    FOREIGN KEY (cart_id) REFERENCES carts(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- Orders table
CREATE TABLE orders (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    order_date DATETIME NOT NULL,
    total_amount DOUBLE NOT NULL,
    status VARCHAR(20) NOT NULL CHECK (status IN ('PENDING', 'CONFIRMED', 'SHIPPED', 'DELIVERED', 'CANCELLED')),
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Order items table
CREATE TABLE order_items (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    price DOUBLE NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);




-- Create a new user (customer)
INSERT INTO users (name, password, role)
VALUES ('john_doe', 'password123', 'CUSTOMER');

-- Create an admin user
INSERT INTO users (name, password, role)
VALUES ('admin', 'admin123', 'ADMIN');

-- Get user by name (login)
SELECT * FROM users WHERE name = 'john_doe' AND password = 'password123';

-- Get all users
SELECT * FROM users;

-- Get user by ID
SELECT * FROM users WHERE id = 1;





-- Add a new product
INSERT INTO products (name, description, price, category)
VALUES ('iPhone 13', 'Latest iPhone model', 999.99, 'Electronics');

-- Update a product
UPDATE products
SET price = 899.99, description = 'Latest iPhone model with updated features'
WHERE id = 1;

-- Delete a product
DELETE FROM products WHERE id = 1;

-- Get all products
SELECT * FROM products;

-- Search products by name
SELECT * FROM products WHERE name LIKE '%iPhone%';

-- Search products by category
SELECT * FROM products WHERE category = 'Electronics';




-- Add stock for a product
INSERT INTO inventory (product_id, quantity)
VALUES (1, 100);

-- Update stock quantity
UPDATE inventory
SET quantity = quantity + 50
WHERE product_id = 1;

-- Get inventory for a product
SELECT p.name, i.quantity
FROM inventory i
JOIN products p ON i.product_id = p.id
WHERE p.id = 1;

-- Get all inventory with product details
SELECT p.name, p.category, i.quantity
FROM inventory i
JOIN products p ON i.product_id = p.id;



-- Create a cart for user
INSERT INTO carts (user_id)
VALUES (1);

-- Add item to cart
INSERT INTO cart_items (cart_id, product_id, quantity)
VALUES (1, 1, 2);

-- Update cart item quantity
UPDATE cart_items
SET quantity = quantity + 1
WHERE cart_id = 1 AND product_id = 1;

-- Remove item from cart
DELETE FROM cart_items
WHERE cart_id = 1 AND product_id = 1;

-- Get cart contents with product details
SELECT p.name, ci.quantity, p.price, (ci.quantity * p.price) as total
FROM cart_items ci
JOIN products p ON ci.product_id = p.id
WHERE ci.cart_id = 1;


-- Create a new order
INSERT INTO orders (user_id, order_date, total_amount, status)
VALUES (1, NOW(), 1999.98, 'PENDING');

-- Add order items
INSERT INTO order_items (order_id, product_id, quantity, price)
VALUES (1, 1, 2, 999.99);

-- Update order status
UPDATE orders
SET status = 'CONFIRMED'
WHERE id = 1;

-- Get order details with items
SELECT o.id, o.order_date, o.status, o.total_amount,
       p.name, oi.quantity, oi.price, (oi.quantity * oi.price) as item_total
FROM orders o
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE o.id = 1;

-- Get user's order history
SELECT o.id, o.order_date, o.status, o.total_amount
FROM orders o
WHERE o.user_id = 1
ORDER BY o.order_date DESC;



https://drive.google.com/file/d/1ExOu9-dlhnqCz8EOQMPgt_b4XtBcMTUs/view?usp=sharing
