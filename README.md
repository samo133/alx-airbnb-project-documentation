# Airbnb Clone — Features & Functionalities (Backend)

**Repository:** `alx-airbnb-project-documentation`  
**Directory:** `features-and-functionalities/`  
**Deliverables:**  
- `README.md` (this file)  
- `features-and-functionalities.png` (Draw.io export)

## 1) Scope & Overview
The backend provides a RESTful (and optional GraphQL) API to manage:
- **User accounts & authentication**
- **Property listings & search**
- **Booking reservations & availability**
- **Payments & receipts**
- **Reviews & ratings**
- **Admin/ops & observability (non-functional)**

> Tech-agnostic blueprint: works with Django/DRF or Node/Express; DB can be PostgreSQL (preferred).

---

## 2) Feature Inventory (Functional Requirements)

### A. User Management & Auth
- User registration, login, logout
- Password hashing & reset (email flow)
- JWT/session-based auth
- Role-based access control (guest, host, admin)
- Profile management (name, contact, avatar)

### B. Property Management
- Create/Read/Update/Delete property
- Address, pricing, availability calendar
- Property images (upload, ordering)
- Amenities (WiFi, Parking, etc.)
- Host dashboard: my listings, booking activity

### C. Search & Discovery
- Filter by location, dates, price range, amenities
- Sorting (price, rating, recency)
- Pagination
- (Optional) fuzzy search / geo-radius search

### D. Booking System
- Create booking (date validation, overlap prevention)
- Booking states: pending, confirmed, cancelled
- Booking history per user & per property
- Host approval (optional)
- Email/notification hooks (async jobs)

### E. Payments
- Initiate payment (Stripe or PayPal)
- Payment intents / retries / refunds
- Store transaction metadata (no raw card data)
- Receipts & payment status webhooks
- (Optional) host payouts (future scope)

### F. Reviews & Ratings
- Post review for a completed stay
- One review per booking per user
- Edit/delete within grace period (optional)
- Rating aggregation per property

### G. Documentation & DX
- OpenAPI/Swagger UI for REST
- GraphQL schema & playground (optional)
- Error codes & examples
- Postman collection (optional)

---

## 3) Non-Functional Requirements (NFRs)
- **Security:** HTTPS/TLS, input validation, authz checks, rate limiting
- **Performance:** caching (Redis), indexed queries, efficient pagination
- **Reliability:** no overlapping bookings at DB level, idempotent payment flows
- **Scalability:** stateless APIs, background jobs (emails, webhooks)
- **Observability:** structured logs, metrics, health checks
- **CI/CD:** automated tests, linting, containerized deploys

---

## 4) Mermaid Overview (renders on GitHub)
```mermaid
flowchart LR
  subgraph Client
    UI[Web/Mobile Client]
  end

  subgraph API
    A[Auth & Users]
    P[Properties]
    S[Search]
    B[Bookings]
    Pay[Payments]
    R[Reviews]
  end

  subgraph Services
    MQ[Async Jobs (Email/Webhooks)]
    Cache[(Redis Cache)]
  end

  subgraph Data
    DB[(PostgreSQL)]
    GW[(Stripe/PayPal)]
  end

  UI --> A & P & S & B & Pay & R
  A --> DB
  P --> DB
  S --> DB
  B --> DB
  R --> DB
  Pay --> GW
  Pay --> MQ
  A & P & S & B & R --> Cache
