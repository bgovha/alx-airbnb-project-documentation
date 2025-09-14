# AirBnB Clone Database System

## Overview
A comprehensive database system for an AirBnB-style accommodation booking platform. This system manages users, properties, bookings, payments, reviews, and messaging functionalities.

## Database Schema

### Entities and Relationships

#### 1. User Entity
Stores all platform users including guests, hosts, and administrators.

**Attributes:**
- `user_id` (Primary Key, UUID) - Unique identifier for each user
- `first_name` (VARCHAR, NOT NULL) - User's first name
- `last_name` (VARCHAR, NOT NULL) - User's last name
- `email` (VARCHAR, UNIQUE, NOT NULL) - Unique email address
- `password_hash` (VARCHAR, NOT NULL) - Hashed password for security
- `phone_number` (VARCHAR, NULL) - Optional phone number
- `role` (ENUM: guest, host, admin, NOT NULL) - User role in the system
- `created_at` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Account creation timestamp

**Indexes:** `user_id`, `email`

#### 2. Property Entity
Manages property listings created by host users.

**Attributes:**
- `property_id` (Primary Key, UUID) - Unique property identifier
- `host_id` (Foreign Key → User.user_id) - Property owner reference
- `name` (VARCHAR, NOT NULL) - Property title
- `description` (TEXT, NOT NULL) - Detailed property description
- `location` (VARCHAR, NOT NULL) - Property address/location
- `pricepernight` (DECIMAL, NOT NULL) - Nightly rental rate
- `created_at` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Listing creation time
- `updated_at` (TIMESTAMP, ON UPDATE CURRENT_TIMESTAMP) - Last update timestamp

**Indexes:** `property_id`, `host_id`

#### 3. Booking Entity
Tracks all property reservations made by guests.

**Attributes:**
- `booking_id` (Primary Key, UUID) - Unique booking identifier
- `property_id` (Foreign Key → Property.property_id) - Booked property reference
- `user_id` (Foreign Key → User.user_id) - Guest user reference
- `start_date` (DATE, NOT NULL) - Booking start date
- `end_date` (DATE, NOT NULL) - Booking end date
- `total_price` (DECIMAL, NOT NULL) - Total booking cost
- `status` (ENUM: pending, confirmed, canceled, NOT NULL) - Booking status
- `created_at` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Booking creation time

**Indexes:** `booking_id`, `property_id`, `user_id`

#### 4. Payment Entity
Manages payment transactions for bookings.

**Attributes:**
- `payment_id` (Primary Key, UUID) - Unique payment identifier
- `booking_id` (Foreign Key → Booking.booking_id) - Associated booking reference
- `amount` (DECIMAL, NOT NULL) - Payment amount
- `payment_date` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Payment timestamp
- `payment_method` (ENUM: credit_card, paypal, stripe, NOT NULL) - Payment method used

**Indexes:** `payment_id`, `booking_id`

#### 5. Review Entity
Stores user reviews and ratings for properties.

**Attributes:**
- `review_id` (Primary Key, UUID) - Unique review identifier
- `property_id` (Foreign Key → Property.property_id) - Reviewed property reference
- `user_id` (Foreign Key → User.user_id) - Review author reference
- `rating` (INTEGER, CHECK: 1-5, NOT NULL) - Property rating (1-5 stars)
- `comment` (TEXT, NOT NULL) - Review text content
- `created_at` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Review creation time

**Indexes:** `review_id`, `property_id`, `user_id`

#### 6. Message Entity
Manages communication between users.

**Attributes:**
- `message_id` (Primary Key, UUID) - Unique message identifier
- `sender_id` (Foreign Key → User.user_id) - Message sender reference
- `recipient_id` (Foreign Key → User.user_id) - Message recipient reference
- `message_body` (TEXT, NOT NULL) - Message content
- `sent_at` (TIMESTAMP, DEFAULT CURRENT_TIMESTAMP) - Message timestamp

**Indexes:** `message_id`, `sender_id`, `recipient_id`

## Relationships

- **User → Property**: One-to-Many (One host can list multiple properties)
- **User → Booking**: One-to-Many (One user can make multiple bookings)
- **Property → Booking**: One-to-Many (One property can have multiple bookings)
- **Booking → Payment**: One-to-One (Each booking has one payment)
- **User → Review**: One-to-Many (One user can write multiple reviews)
- **Property → Review**: One-to-Many (One property can receive multiple reviews)
- **User → Message**: Many-to-Many (Users can send/receive multiple messages)

## Constraints

### User Table
- Unique constraint on email
- Non-null constraints on required fields (first_name, last_name, email, password_hash, role)

### Property Table
- Foreign key constraint on host_id referencing User.user_id
- Non-null constraints on essential attributes

### Booking Table
- Foreign key constraints on property_id and user_id
- Status must be one of: pending, confirmed, or canceled

### Payment Table
- Foreign key constraint on booking_id ensuring payment links to valid bookings

### Review Table
- Rating values constrained between 1-5
- Foreign key constraints on property_id and user_id

### Message Table
- Foreign key constraints on sender_id and recipient_id

## Indexing Strategy

**Primary Keys:** All automatically indexed (user_id, property_id, booking_id, payment_id, review_id, message_id)

**Additional Indexes:**
- `email` in User table for fast login lookups
- `property_id` in Property and Booking tables for property-related queries
- `booking_id` in Booking and Payment tables for booking management
- `user_id` across multiple tables for user activity tracking

## Usage Examples

### Creating a New User
```sql
INSERT INTO User (user_id, first_name, last_name, email, password_hash, role)
VALUES (UUID(), 'John', 'Doe', 'john@example.com', 'hashed_password', 'guest');
```

### Listing a New Property
```sql
INSERT INTO Property (property_id, host_id, name, description, location, pricepernight)
VALUES (UUID(), 'host_uuid', 'Cozy Apartment', 'Beautiful downtown apartment', 'New York', 99.99);
```

### Making a Booking
```sql
INSERT INTO Booking (booking_id, property_id, user_id, start_date, end_date, total_price, status)
VALUES (UUID(), 'property_uuid', 'user_uuid', '2024-01-15', '2024-01-20', 499.95, 'confirmed');
```

## Technology Stack

- **Database:** MySQL/PostgreSQL (supports UUID, ENUM, and constraints)
- **Data Types:** UUID, VARCHAR, TEXT, DECIMAL, DATE, TIMESTAMP, ENUM
- **Security:** Password hashing, constraint validation
- **Performance:** Strategic indexing for common queries

## Installation & Setup

1. Create database with appropriate character set
2. Execute schema creation script
3. Set up application-level password hashing
4. Configure database connection parameters

## Maintenance

- Regular backup of database
- Monitor performance using query analytics
- Consider partitioning for large tables (Bookings, Messages)
- Implement archiving strategy for historical data

This database schema provides a robust foundation for an AirBnB-style platform with proper normalization, constraints, and performance considerations.