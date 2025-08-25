# Airbnb Clone – Backend Requirement Specifications

This document outlines the **technical and functional requirements** for the Airbnb Clone backend. It covers three major features: **User Authentication**, **Property Management**, and **Booking System**.

---

## 1. User Authentication

### Functional Requirements
- Users must be able to **register, log in, and log out**.
- Authentication will be managed via **JWT (JSON Web Tokens)**.
- Passwords must be **hashed** using bcrypt before storage.
- The system must support **role-based access control** (Guest, Host, Admin).

### API Endpoints
| Method | Endpoint               | Description              | Auth Required |
|--------|------------------------|--------------------------|---------------|
| POST   | `/api/v1/auth/register` | Register new user       | No            |
| POST   | `/api/v1/auth/login`    | Login and get JWT token | No            |
| POST   | `/api/v1/auth/logout`   | Invalidate token        | Yes           |
| GET    | `/api/v1/auth/me`       | Get current user profile| Yes           |

### Input/Output Example
**Register (POST /auth/register)**
- **Input (JSON):**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "StrongPass123!"
}
```

- **Output (201 Created):**
```json
{
  "id": "u123",
  "name": "John Doe",
  "email": "john@example.com",
  "role": "guest",
  "token": "jwt_token_here"
}
```

### Validation Rules
- Email must be valid and unique.
- Password must be at least 8 characters, with letters and numbers.

### Performance Criteria
- Response time must be **< 300ms** for authentication requests.
- JWT tokens must expire after **24 hours**.

---

## 2. Property Management

### Functional Requirements
- Hosts must be able to **list, update, and delete properties**.
- Properties must include: title, description, location, price per night, and availability.
- Guests must be able to **search properties** by location, price range, and availability.

### API Endpoints
| Method | Endpoint                   | Description            | Auth Required | Role  |
|--------|----------------------------|------------------------|---------------|-------|
| POST   | `/api/v1/properties`       | Create new property    | Yes           | Host  |
| GET    | `/api/v1/properties`       | List/search properties | No            | Any   |
| GET    | `/api/v1/properties/:id`   | Get property details   | No            | Any   |
| PUT    | `/api/v1/properties/:id`   | Update property        | Yes           | Host  |
| DELETE | `/api/v1/properties/:id`   | Delete property        | Yes           | Host  |

### Input/Output Example
**Create Property (POST /properties)**
- **Input (JSON):**
```json
{
  "title": "Cozy Apartment",
  "description": "2 bed apartment near city center",
  "location": "Lagos, Nigeria",
  "price_per_night": 100,
  "availability": true
}
```

- **Output (201 Created):**
```json
{
  "id": "p456",
  "title": "Cozy Apartment",
  "location": "Lagos, Nigeria",
  "price_per_night": 100,
  "availability": true
}
```

### Validation Rules
- Title and description are required.
- Price must be a positive integer.

### Performance Criteria
- Must support search queries filtered by price and location.
- Should handle **1000 concurrent property searches** with < 500ms response time.

---

## 3. Booking System

### Functional Requirements
- Guests must be able to **book available properties**.
- Hosts must be able to **view bookings** for their properties.
- Admin must be able to **manage all bookings**.
- Bookings must include **dates, total cost, and payment status**.

### API Endpoints
| Method | Endpoint                 | Description              | Auth Required | Role   |
|--------|--------------------------|--------------------------|---------------|--------|
| POST   | `/api/v1/bookings`       | Create a booking         | Yes           | Guest  |
| GET    | `/api/v1/bookings`       | List bookings            | Yes           | Any    |
| GET    | `/api/v1/bookings/:id`   | Get booking details      | Yes           | Any    |
| PUT    | `/api/v1/bookings/:id`   | Update booking (cancel)  | Yes           | Guest/Admin |
| DELETE | `/api/v1/bookings/:id`   | Cancel booking           | Yes           | Guest/Admin |

### Input/Output Example
**Create Booking (POST /bookings)**
- **Input (JSON):**
```json
{
  "property_id": "p456",
  "check_in": "2025-09-01",
  "check_out": "2025-09-05"
}
```

- **Output (201 Created):**
```json
{
  "id": "b789",
  "property_id": "p456",
  "guest_id": "u123",
  "check_in": "2025-09-01",
  "check_out": "2025-09-05",
  "total_cost": 400,
  "status": "confirmed"
}
```

### Validation Rules
- Dates must be valid and `check_out` > `check_in`.
- Property must be available for the given range.

### Performance Criteria
- Double booking must be prevented using **transaction locks** or **unique constraints**.
- Must process **100 concurrent bookings per second** without failure.

---

## Summary

This document defines the **requirements** for three core backend features of the Airbnb Clone:
1. **User Authentication** (secure login/register)
2. **Property Management** (listing and searching properties)
3. **Booking System** (reserving and managing bookings)

These requirements will serve as the foundation for implementing the backend API.
