# Airbnb Clone Project

Airbnb Clone Project is a comprehensive, real-world application designed to simulate the development of a robust booking platform like Airbnb.

## Team Roles
- **ET-Touil Abdelilah**: DevOps Enggineer 

## Technology Stack
<!-- 
- Frontend: 
- Backend: 
- Database: 
- Authentication: 
-->
## Database Design

---

# Entities and Attributes

## 1. User

Represents guests, hosts, and administrators.

| Column | Type | Constraints |
|---|---|---|
| user_id | UUID | Primary Key, Indexed |
| first_name | VARCHAR(50) | NOT NULL |
| last_name | VARCHAR(50) | NOT NULL |
| email | VARCHAR(100) | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | NOT NULL |
| phone_number | VARCHAR(20) | NULL |
| role | ENUM('guest', 'host', 'admin') | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### Constraints
- Unique constraint on `email`
- Required fields cannot be NULL

---

## 2. Property

Represents properties listed by hosts.

| Column | Type | Constraints |
|---|---|---|
| property_id | UUID | Primary Key, Indexed |
| host_id | UUID | Foreign Key → User(user_id) |
| name | VARCHAR(100) | NOT NULL |
| description | TEXT | NOT NULL |
| location | VARCHAR(255) | NOT NULL |
| price_per_night | DECIMAL(10,2) | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP |

### Relationships
- One host can own many properties
- One property belongs to one host

---

## 3. Booking

Represents reservations made by users.

| Column | Type | Constraints |
|---|---|---|
| booking_id | UUID | Primary Key, Indexed |
| property_id | UUID | Foreign Key → Property(property_id) |
| user_id | UUID | Foreign Key → User(user_id) |
| start_date | DATE | NOT NULL |
| end_date | DATE | NOT NULL |
| total_price | DECIMAL(10,2) | NOT NULL |
| status | ENUM('pending', 'confirmed', 'canceled') | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### Constraints
- `start_date < end_date`
- Status must be:
  - `pending`
  - `confirmed`
  - `canceled`

---

## 4. Payment

Represents payment transactions for bookings.

| Column | Type | Constraints |
|---|---|---|
| payment_id | UUID | Primary Key, Indexed |
| booking_id | UUID | Foreign Key → Booking(booking_id) |
| amount | DECIMAL(10,2) | NOT NULL |
| payment_date | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| payment_method | ENUM('credit_card', 'paypal', 'stripe') | NOT NULL |

### Relationships
- One booking can have one or more payments

---

## 5. Review

Represents user feedback on properties.

| Column | Type | Constraints |
|---|---|---|
| review_id | UUID | Primary Key, Indexed |
| property_id | UUID | Foreign Key → Property(property_id) |
| user_id | UUID | Foreign Key → User(user_id) |
| rating | INTEGER | CHECK (rating BETWEEN 1 AND 5) |
| comment | TEXT | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### Constraints
- Rating must be between 1 and 5

---

## 6. Message

Represents direct communication between users.

| Column | Type | Constraints |
|---|---|---|
| message_id | UUID | Primary Key, Indexed |
| sender_id | UUID | Foreign Key → User(user_id) |
| recipient_id | UUID | Foreign Key → User(user_id) |
| message_body | TEXT | NOT NULL |
| sent_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

# Relationships

| Relationship | Type |
|---|---|
| User → Property | One-to-Many |
| User → Booking | One-to-Many |
| Property → Booking | One-to-Many |
| Booking → Payment | One-to-Many |
| User → Review | One-to-Many |
| Property → Review | One-to-Many |
| User → Message | One-to-Many |

---

# Constraints

## User Table
- Unique constraint on `email`
- Non-null required fields

## Property Table
- Foreign key constraint on `host_id`

## Booking Table
- Foreign key constraints on:
  - `property_id`
  - `user_id`
- Booking status restricted to:
  - `pending`
  - `confirmed`
  - `canceled`

## Payment Table
- Foreign key constraint on `booking_id`

## Review Table
- Rating constraint:
```sql
CHECK (rating BETWEEN 1 AND 5)
```

## Message Table
- Foreign key constraints on:
  - `sender_id`
  - `recipient_id`

---

# Indexing Strategy

## Automatically Indexed
- All Primary Keys

## Additional Indexes

| Table | Indexed Column |
|---|---|
| User | email |
| Property | host_id |
| Property | location |
| Booking | property_id |
| Booking | user_id |
| Booking | status |
| Payment | booking_id |
| Review | property_id |
| Message | sender_id |
| Message | recipient_id |

---

# Additional Recommended Constraints

## Booking Date Validation

```sql
CHECK (start_date < end_date)
```

## Positive Values

```sql
CHECK (price_per_night > 0)
CHECK (total_price > 0)
CHECK (amount > 0)
```

## Prevent Duplicate Reviews

```sql
UNIQUE(property_id, user_id)
```

---

# Database Normalization

This schema follows:

- First Normal Form (1NF)
- Second Normal Form (2NF)
- Third Normal Form (3NF)

---

# ER Diagram Overview

```text
User
 ├── Property
 ├── Booking
 ├── Review
 └── Message

Property
 ├── Booking
 └── Review

Booking
 └── Payment
```

---

# Technologies

- Relational Database Management System (RDBMS)
- SQL
- UUID-based primary keys
- Indexed foreign keys for optimization

---
## Feature Breakdown



---
## API Security



### 1. Authentication
Only registered users can access protected routes using secure login systems (e.g., JWT or OAuth). Authentication helps verify the identity of users and prevents unauthorized access to accounts and sensitive operations.

### 2. Authorization
Different levels of access are enforced (e.g., guest, host, admin). Authorization ensures that users can only perform actions they're permitted to (e.g., a user can’t delete another user's listing).

### 3. Rate Limiting
Rate limiting protects the backend from abuse by limiting the number of API requests a user can make within a specific timeframe. This helps mitigate brute-force attacks and reduce server load.

### 4. Input Validation & Sanitization
All incoming data will be validated and sanitized to prevent injection attacks (e.g., SQL injection, XSS). This ensures that user input doesn't compromise the database or application behavior.

### 5. HTTPS Enforcement
All communication with the backend will use HTTPS to encrypt data in transit. This protects sensitive data like login credentials and payment information from being intercepted.

### 6. Secure Data Storage
Sensitive data such as passwords will be hashed (e.g., using bcrypt) before storage. Storing passwords securely protects users in case of a database breach.

### 7. Error Handling
Error messages will be managed to avoid exposing stack traces or internal system details. Proper error handling improves security by not leaking information that attackers could use.

Security is crucial in this project to protect user personal data, maintain system stability, secure financial transactions, and build user trust in the platform.

## CI/CD Pipeline

CI/CD (Continuous Integration and Continuous Deployment) pipelines automate the process of testing, building, and deploying code changes. They help ensure that new features or bug fixes are integrated smoothly without breaking the existing functionality.






