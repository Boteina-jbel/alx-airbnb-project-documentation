# 🏗️ Airbnb Clone Backend – Requirement Specifications

## 🎯 Objective
This document outlines the technical and functional requirements for core backend features of the Airbnb Clone system, including detailed API endpoints, input/output formats, validation rules, and performance expectations.

---

## 1. 🔐 User Authentication

### 🧩 Functional Requirements
- Allow users (Guests and Hosts) to register, log in, and manage sessions securely.
- Support JWT-based authentication and OAuth (Google, Facebook) login.

### 📌 API Endpoints

#### ✅ POST /api/v1/auth/register
- Registers a new user

**Input (JSON):**
```json
{
  "name": "Boteina Jbel",
  "email": "boteina@example.com",
  "password": "StrongPassword123",
  "role": "guest"
}
```

**Validations:**
- Email must be unique and valid.
- Password must be at least 8 characters.

**Output:**
```json
{
  "message": "User registered successfully",
  "token": "<jwt_token>"
}
```

---

#### ✅ POST /api/v1/auth/login
- Authenticates user and returns JWT token

**Input (JSON):**
```json
{
  "email": "boteina@example.com",
  "password": "StrongPassword123"
}
```

**Output:**
```json
{
  "message": "Login successful",
  "token": "<jwt_token>"
}
```

---

### 🛡️ Performance & Security
- Tokens expire in 1 hour; refresh token support optional.
- Passwords hashed using bcrypt.
- Rate limiting on login endpoint.

---

## 2. 🏡 Property Management

### 🧩 Functional Requirements
- Hosts can create, update, delete, and view property listings.
- Each listing includes title, location, price, description, amenities, and photos.

### 📌 API Endpoints

#### ✅ POST /api/v1/properties
- Host creates a property listing

**Input (JSON):**
```json
{
  "title": "Charming Riad in Marrakech",
  "description": "Traditional Moroccan Riad...",
  "location": "Marrakech",
  "price_per_night": 75,
  "max_guests": 4,
  "amenities": ["Wi-Fi", "AC", "Pool"]
}
```

**Validations:**
- Title is required.
- Price must be a positive number.
- Amenities must be a list of strings.

**Output:**
```json
{
  "message": "Property created",
  "property_id": "prop_123"
}
```

---

#### ✅ GET /api/v1/properties?location=Marrakech&min_price=50
- Users search for listings with filters

**Output:**
```json
[
  {
    "id": "prop_123",
    "title": "Charming Riad in Marrakech",
    "price_per_night": 75,
    "location": "Marrakech"
  }
]
```

---

### 🛡️ Performance & Optimization
- Indexed search fields: `location`, `price`, `availability`.
- Use Redis caching for frequent search queries.

---

## 3. 📅 Booking System

### 🧩 Functional Requirements
- Guests can book available properties for specified dates.
- Prevent double bookings and track status (pending, confirmed, cancelled).

### 📌 API Endpoints

#### ✅ POST /api/v1/bookings
- Guest books a property

**Input (JSON):**
```json
{
  "property_id": "prop_123",
  "start_date": "2025-08-01",
  "end_date": "2025-08-05"
}
```

**Validations:**
- Dates must be in the future.
- End date must be after start date.
- Check for overlapping bookings.

**Output:**
```json
{
  "message": "Booking created",
  "booking_id": "book_456",
  "status": "pending"
}
```

---

#### ✅ DELETE /api/v1/bookings/book_456
- Guest cancels a booking (before start date)

**Output:**
```json
{
  "message": "Booking canceled successfully"
}
```

---

### 🛡️ Performance
- Transactions used to avoid race conditions when booking the same date range.
- Bookings table indexed by `property_id`, `start_date`, `end_date`.

---

## 📚 References

- [JWT Authentication - Auth0 Docs](https://auth0.com/docs/authenticate/tokens/json-web-tokens)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [RESTful API Best Practices](https://restfulapi.net/)
- [Stripe API Reference](https://stripe.com/docs/api)
- [Rate Limiting in APIs – MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Retry-After)
