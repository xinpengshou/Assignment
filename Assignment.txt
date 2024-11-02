-- Create database
CREATE DATABASE LittleLemon;
USE LittleLemon;

-- Create table for customers
CREATE TABLE Customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone_number VARCHAR(15),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create table for restaurant tables (seating arrangements)
CREATE TABLE Tables (
    table_id INT AUTO_INCREMENT PRIMARY KEY,
    table_number INT UNIQUE NOT NULL,
    capacity INT NOT NULL,
    location VARCHAR(50) -- For example, "indoors", "outdoors", etc.
);

-- Create table for bookings
CREATE TABLE Bookings (
    booking_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    table_id INT NOT NULL,
    booking_date DATE NOT NULL,
    booking_time TIME NOT NULL,
    num_guests INT NOT NULL,
    status ENUM('confirmed', 'canceled', 'completed') DEFAULT 'confirmed',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id),
    FOREIGN KEY (table_id) REFERENCES Tables(table_id)
);

-- Create table for payments
CREATE TABLE Payments (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    booking_id INT NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    payment_date DATE NOT NULL,
    payment_method ENUM('credit_card', 'debit_card', 'cash', 'online'),
    status ENUM('paid', 'pending', 'failed') DEFAULT 'pending',
    FOREIGN KEY (booking_id) REFERENCES Bookings(booking_id)
);

-- Sample procedures for booking system functionalities

-- GetMaxQuantity: Get the maximum number of guests a table can accommodate
DELIMITER //
CREATE PROCEDURE GetMaxQuantity()
BEGIN
    SELECT MAX(capacity) AS max_capacity FROM Tables;
END //
DELIMITER ;

-- ManageBooking: Update booking status
DELIMITER //
CREATE PROCEDURE ManageBooking(IN book_id INT, IN new_status ENUM('confirmed', 'canceled', 'completed'))
BEGIN
    UPDATE Bookings SET status = new_status WHERE booking_id = book_id;
END //
DELIMITER ;

-- UpdateBooking: Update details of an existing booking
DELIMITER //
CREATE PROCEDURE UpdateBooking(IN book_id INT, IN new_date DATE, IN new_time TIME, IN new_num_guests INT)
BEGIN
    UPDATE Bookings
    SET booking_date = new_date, booking_time = new_time, num_guests = new_num_guests
    WHERE booking_id = book_id;
END //
DELIMITER ;

-- AddBooking: Add a new booking
DELIMITER //
CREATE PROCEDURE AddBooking(IN cust_id INT, IN tbl_id INT, IN book_date DATE, IN book_time TIME, IN guests INT)
BEGIN
    INSERT INTO Bookings (customer_id, table_id, booking_date, booking_time, num_guests, status)
    VALUES (cust_id, tbl_id, book_date, book_time, guests, 'confirmed');
END //
DELIMITER ;

-- CancelBooking: Cancel a booking
DELIMITER //
CREATE PROCEDURE CancelBooking(IN book_id INT)
BEGIN
    UPDATE Bookings SET status = 'canceled' WHERE booking_id = book_id;
END //
DELIMITER ;

-- Verify that tables are created
SHOW TABLES;

-- To fill tables with sample data, you can use the uploaded Excel data or manually add entries as needed.
