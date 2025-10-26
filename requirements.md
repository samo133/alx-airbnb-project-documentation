# Airbnb Clone — Backend Requirement Specifications

**Repository:** `alx-airbnb-project-documentation`  
**File:** `requirements.md`  
**Scope:** Technical + functional requirements for **User Authentication**, **Property Management**, and **Booking System**.  
**API Style:** REST (JSON over HTTPS). GraphQL may be added later.

---

## 0) Global Conventions

- **Base URL:** `/api/v1`
- **Content-Type:** `application/json; charset=utf-8`
- **Auth:** Bearer JWT in `Authorization: Bearer <token>`
- **Time:** ISO-8601 (UTC), dates as `YYYY-MM-DD`
- **Ids:** UUIDv4 strings
- **Pagination:** `?page=<int>&page_size=<1..100>` (default: 20)
- **Sorting/Filtering:** predictable query params (`?city=Berlin&min_price=50&sort=price_asc`)
- **Rate Limits (MVP):** 60 req/min per IP for unauth, 300 req/min per token for auth
- **Errors:** JSON problem details
  ```json
  { "error": "validation_error", "message": "email is required", "fields": { "email": "required" } }
````

---

## 1) User Authentication

### 1.1 Functional Requirements

* Register a new user with unique `email` and `username`.
* Login with email/username + password to receive a **JWT** access token.
* Get/update own profile (name, avatar, phone).
* Passwords **must be hashed** (e.g., bcrypt/argon2).
* Optional: password reset via email flow (out of scope for MVP endpoints below).

### 1.2 Endpoints

#### POST `/auth/register`

* **Body**

  ```json
  { "username": "osama", "email": "osama@example.com", "password": "Strong#Pass123" }
  ```
* **201 Created**

  ```json
  { "id":"<uuid>", "username":"osama", "email":"osama@example.com", "created_at":"2025-10-26T12:00:00Z" }
  ```
* **Validation**

  * `email`: valid, unique
  * `username`: 3–30 chars, unique, `[a-z0-9_-.]`
  * `password`: ≥ 10 chars, mixed classes
* **Errors:** `409 conflict` (email/username in use), `400` (invalid)

#### POST `/auth/login`

* **Body**

  ```json
  { "email":"osama@example.com", "password":"Strong#Pass123" }
  ```
* **200 OK**

  ```json
  { "access_token":"<jwt>", "token_type":"bearer", "expires_in":3600 }
  ```
* **Errors:** `401` (invalid credentials), `429` (too many attempts)

#### GET `/users/me`  *(auth)*

* **200 OK**

  ```json
  { "id":"<uuid>", "username":"osama", "email":"osama@example.com", "role":"guest", "date_joined":"2025-02-01T10:00:00Z", "avatar_url":null, "phone":null }
  ```

#### PATCH `/users/me` *(auth)*

* **Body (any subset)**

  ```json
  { "username":"osama133", "avatar_url":"https://.../avatar.png", "phone":"+4912345678" }
  ```
* **Constraints:** username uniqueness; `phone` E.164.

### 1.3 Non-Functional & Security

* Store only password **hash**; enforce password policy.
* Lockout/backoff after 5 failed attempts for 15 minutes.
* JWT expiration ≤ 1h; refresh tokens optional (future).
* PII access restricted to resource owner/admin.
* **Perf:** Register/Login < 150 ms p95 (ex-network), GET `/users/me` < 100 ms p95.

---

## 2) Property Management

### 2.1 Functional Requirements

* Hosts can **create/update/delete** their properties.
* Anyone can **list** and **view** properties.
* Support images and amenities (MVP: image URLs).
* Basic search/filter by city/price/date availability (availability check uses Booking store).

### 2.2 Endpoints

#### GET `/properties`

* **Query:** `city`, `min_price`, `max_price`, `amenities`, `page`, `page_size`, `sort` (`price_asc`, `created_desc`)
* **200 OK (paginated)**

  ```json
  {
    "items": [
      { "id":"<uuid>", "title":"Sunny Loft", "city":"Berlin", "country_code":"DE", "price_per_night":120.0, "rating":4.8, "images":["https://.../1.jpg"] }
    ],
    "page":1, "page_size":20, "total":120
  }
  ```

#### POST `/properties` *(auth: host)*

* **Body**

  ```json
  {
    "title":"Sunny Loft in Berlin",
    "description":"Bright loft...",
    "address": { "line":"Alexanderplatz 1", "city":"Berlin", "region":"BE", "postal_code":"10178", "country_code":"DE" },
    "price_per_night":120.0,
    "amenities":["wifi","kitchen"],
    "images":["https://.../1.jpg","https://.../2.jpg"]
  }
  ```
* **201 Created**

  ```json
  { "id":"<uuid>", "owner_id":"<uuid>", "created_at":"2025-10-26T12:00:00Z" }
  ```
* **Validation**

  * `title` 3–120; `price_per_night` ≥ 0.
  * `country_code`: ISO-3166-1 alpha-2.
  * `amenities`: known codes.

#### GET `/properties/{property_id}`

* **200 OK**

  ```json
  {
    "id":"<uuid>", "owner_id":"<uuid>",
    "title":"Sunny Loft in Berlin",
    "description":"Bright loft...",
    "address": { "line":"Alexanderplatz 1","city":"Berlin","region":"BE","postal_code":"10178","country_code":"DE" },
    "price_per_night":120.0,
    "amenities":["wifi","kitchen","washer"],
    "images":["https://.../1.jpg","https://.../2.jpg"],
    "created_at":"2025-01-01T10:00:00Z"
  }
  ```

#### PATCH `/properties/{property_id}` *(auth: owner)*

* Partial update; same validation as create.

#### DELETE `/properties/{property_id}` *(auth: owner/admin)*

* **204 No Content**

### 2.3 Non-Functional & Security

* Only **owner** (or admin) can mutate a property.
* Inputs sanitized; length limits on text fields; strict URL validation for images.
* **Indexes:** `(city)`, `(country_code)`, composite `(price_per_night, city)`.
* **Cache:** hot reads via Redis; invalidate on mutation.
* **Perf:** List (cold) < 250 ms p95; cached < 50 ms p95; Get by id < 120 ms p95.

---

## 3) Booking System

### 3.1 Functional Requirements

* Authenticated users can create/cancel bookings.
* Prevent overlapping bookings for the same property/dates.
* Booking lifecycle: `pending → confirmed → cancelled`.
* Confirmation driven by **payment success** (via Payments service/webhook); if Payments not yet implemented, allow manual confirm for test.

### 3.2 Endpoints

#### POST `/bookings` *(auth)*

* **Body**

  ```json
  { "property_id":"<uuid>", "check_in_date":"2025-03-10", "check_out_date":"2025-03-14" }
  ```
* **Flow (service level)**

  1. Validate date range (`check_in_date` < `check_out_date`, min stay rules optional).
  2. Check availability (no overlap in DB).
  3. Create booking with `status="pending"` and price snapshot (optional).
* **201 Created**

  ```json
  { "id":"<uuid>", "status":"pending", "total_estimate":514.80 }
  ```
* **Errors:** `400` (invalid date/format), `404` (property not found), `409` (dates unavailable)

#### GET `/bookings/{booking_id}` *(auth: owner of booking or host of property or admin)*

* **200 OK**

  ```json
  {
    "id":"<uuid>", "user_id":"<uuid>", "property_id":"<uuid>",
    "check_in_date":"2025-03-10", "check_out_date":"2025-03-14",
    "status":"confirmed", "created_at":"2025-02-01T10:00:00Z"
  }
  ```

#### GET `/bookings` *(auth)*

* **Query:** `status`, `property_id`, `from`, `to`, pagination
* Returns **only user’s bookings** unless role=host (then bookings for owned properties) or admin.

#### POST `/bookings/{booking_id}/cancel` *(auth: booker or host/admin)*

* **200 OK**

  ```json
  { "id":"<uuid>", "status":"cancelled" }
  ```
* **Rules:** cannot cancel past completed stays; apply policy hooks for refunds.

### 3.3 Validation Rules

* Dates: valid ISO; `check_in_date` ≥ today (configurable); `check_out_date` ≥ `check_in_date + 1`.
* Availability check is **authoritative** (DB constraint + service check).
* Property must be active; owner cannot book own property (optional rule).

### 3.4 Non-Functional & Performance

* **Overlap prevention:** DB constraint (e.g., `EXCLUDE` with date range) + service-level check.
* **Cache invalidation:** clear relevant property availability keys after create/cancel.
* **Idempotency:** `POST /bookings` may accept `Idempotency-Key` header to avoid dupes.
* **Perf:** Create booking < 200 ms p95 (ex-payment); Get booking < 100 ms p95.

---

## 4) Common Responses

### 4.1 Error Catalogue

* `400 validation_error` — invalid body/params
* `401 unauthorized` — missing/invalid token
* `403 forbidden` — lacks permission (e.g., not owner)
* `404 not_found` — resource absent
* `409 conflict` — booking overlap, duplicate username/email
* `429 too_many_requests` — rate limited
* `500 internal_error` — unexpected server fault

### 4.2 Health & Readiness (optional for NFRs)

* `GET /health` → `{ "status":"ok" }`
* `GET /ready` → checks DB/cache connections

---

## 5) Acceptance Criteria (MVP)

* **Auth:** Users can register, login, fetch/update their profile; password hashing enforced; unique email/username.
* **Properties:** Anonymous listing and detail; host-only create/update/delete; validations and indexing in place.
* **Bookings:** Users can create and cancel bookings; overlaps rejected; role-based access respected.
* **Non-Functional:** Rate limiting, input validation/sanitization, structured error payloads, pagination on list endpoints; p95 latencies within stated budgets.

---

## 6) Out of Scope (for now)

* Payments & refunds APIs (separate spec)
* Reviews endpoints (separate spec)
* Admin moderation endpoints
* Full text search / geo-radius search
* Websockets/real-time updates




