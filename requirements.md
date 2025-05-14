# Technical Requirements for Airbnb Clone (Backend)

## 1. User Authentication
### Functional Requirements:
- Users (Guests/Hosts/Admins) can register, log in, and log out.
- Password reset via email.
- Role-based access control (Guest, Host, Admin).

### Technical Specifications:
#### API Endpoints:
- `POST /api/auth/register`  
  **Input**: `{ name, email, password, role }`  
  **Output**: `{ user_id, email, role, auth_token }`  
- `POST /api/auth/login`  
  **Input**: `{ email, password }`  
  **Output**: `{ user_id, role, auth_token }`  
- `POST /api/auth/logout`  
  **Headers**: `Authorization: Bearer <token>`  

#### Validation Rules:
- Email: Valid format (regex), unique.
- Password: Min 8 chars, at least 1 number/special char.
- Role: Enum (`guest`, `host`, `admin`).

#### Performance Criteria:
- Response time: < 500ms.
- Handle 1000 concurrent logins/sec.

---

## 2. Property Management (Host)
### Functional Requirements:
- Hosts can create, update, delete property listings.
- Admins can verify listings.
- Guests can view/search listings.

### Technical Specifications:
#### API Endpoints:
- `POST /api/properties` (Host)  
  **Input**: `{ title, description, price, location, amenities[] }`  
  **Output**: `{ property_id, status: "pending_verification" }`  
- `PUT /api/properties/:id` (Host/Admin)  
  **Input**: `{ title, price, status }`  
- `GET /api/properties?location=...&price_max=...` (Guest)  
  **Output**: `[{ property_id, title, price, location, rating }]`  

#### Validation Rules:
- Price: Positive number, max 2 decimal places.
- Location: Required, geocoded (lat/long).
- Status: Enum (`pending`, `verified`, `rejected`).

#### Performance Criteria:
- Search results in < 1s (with pagination).
- Support 10,000 active listings.

---

## 3. Booking System
### Functional Requirements:
- Guests can book properties for date ranges.
- Hosts can confirm/reject bookings.
- Payments processed via Stripe/PayPal.

### Technical Specifications:
#### API Endpoints:
- `POST /api/bookings` (Guest)  
  **Input**: `{ property_id, start_date, end_date, payment_method }`  
  **Output**: `{ booking_id, total_price, status: "pending" }`  
- `PUT /api/bookings/:id/confirm` (Host)  
  **Headers**: `Authorization: Bearer <token>`  
  **Output**: `{ booking_id, status: "confirmed" }`  

#### Validation Rules:
- Date range: Valid, no overlaps with existing bookings.
- Payment: Tokenized (Stripe API integration).

#### Performance Criteria:
- Booking creation: < 800ms.
- Handle 500 bookings/minute.

---

## Error Handling
- **HTTP Codes**:  
  - `400` for invalid inputs.  
  - `401` for unauthorized actions.  
  - `404` for missing resources.  
- **Response Format**:  
  ```json
  { "error": "Invalid date range", "details": "Dates overlap with existing booking." }

---

## Appendix
- **Tools**: Node.js/Express (backend), PostgreSQL (DB), JWT (auth).

- **Testing**: Postman/Thunder Client for API testing.

---
