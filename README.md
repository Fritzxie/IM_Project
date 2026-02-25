# IM_Project
CREATE DATABASE FlightBooking_And_ReservationSystem;
USE FlightBooking_And_ReservationSystem;

CREATE TABLE Customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(50),
    passport_num VARCHAR(50)
);

CREATE TABLE Airports (
    airport_id INT PRIMARY KEY AUTO_INCREMENT,
    airport_name VARCHAR(100) NOT NULL,
    city VARCHAR(100),
    country VARCHAR(100)
);

CREATE TABLE Flights (
    flight_id INT PRIMARY KEY AUTO_INCREMENT,
    flight_num VARCHAR(50) NOT NULL,
    departure_airport_id INT NOT NULL,
    arrival_airport_id INT NOT NULL,
    departure_time DATETIME NOT NULL,
    arrival_time DATETIME NOT NULL,
    base_price DECIMAL(10,2) NOT NULL,
    total_seats INT NOT NULL,

    CHECK (arrival_time > departure_time),

    FOREIGN KEY (departure_airport_id) REFERENCES Airports(airport_id),
    FOREIGN KEY (arrival_airport_id) REFERENCES Airports(airport_id)
);

CREATE TABLE Seats (
    seat_id INT PRIMARY KEY AUTO_INCREMENT,
    flight_id INT NOT NULL,
    seat_number VARCHAR(10) NOT NULL,
    seat_class ENUM('Economy','Business') DEFAULT 'Economy',
    status ENUM('Available','Booked') DEFAULT 'Available',

    FOREIGN KEY (flight_id) REFERENCES Flights(flight_id)
        ON DELETE CASCADE,
    UNIQUE (flight_id, seat_number)
);

CREATE TABLE Bookings (
    booking_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    flight_id INT NOT NULL,
    seat_id INT NOT NULL,
    booking_date DATETIME NOT NULL,
    total_fare DECIMAL(10,2) NOT NULL,
    booking_status ENUM('Confirmed','Cancelled') DEFAULT 'Confirmed',

    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id),
    FOREIGN KEY (flight_id) REFERENCES Flights(flight_id),
    FOREIGN KEY (seat_id) REFERENCES Seats(seat_id),

    UNIQUE (seat_id)  -- prevents double booking of the same seat
);

CREATE TABLE Payments (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    booking_id INT NOT NULL,
    payment_date DATETIME NOT NULL,
    payment_method VARCHAR(50),
    amount DECIMAL(10,2),
    payment_status ENUM('Paid','Pending','Failed'),

    FOREIGN KEY (booking_id) REFERENCES Bookings(booking_id)
        ON DELETE CASCADE
);

CREATE TABLE Cancellations (
    cancellation_id INT PRIMARY KEY AUTO_INCREMENT,
    booking_id INT NOT NULL,
    cancellation_date DATETIME NOT NULL,
    refund_amount DECIMAL(10,2),
    cancellation_fee DECIMAL(10,2),
    reason VARCHAR(300),

    FOREIGN KEY (booking_id) REFERENCES Bookings(booking_id)
        ON DELETE CASCADE
);
