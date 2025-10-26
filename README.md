# Airbnb Clone — Use Case Diagram

**Repository:** `alx-airbnb-project-documentation`  
**Directory:** `use-case-diagram/`  
**Deliverables:**  
- `README.md` (this file)  
- `use-case-diagram.png` (Draw.io export)

## 1) Purpose
Visualize how key actors interact with the backend system across the main capabilities: authentication, listings, bookings, payments, and reviews.

---

## 2) Actors
- **Guest** — Unauthenticated user browsing and registering
- **Authenticated User (Guest)** — Book, pay, review, manage profile
- **Host** — Create and manage listings, approve/cancel bookings (if required)
- **Admin** — Moderate content, oversee users/system
- **Payment Gateway (External)** — Stripe/PayPal integration
- **Notification Service (External)** — Emails/webhooks for confirmations

---

## 3) Primary Use Cases
- **User Registration & Login** (Auth)
- **Profile Management** (Auth)
- **Browse/Search Properties** (Listings)
- **Create/Manage Property** (Host)
- **Upload Property Images** (Host)
- **Manage Availability** (Host)
- **Create Booking / Cancel Booking** (Bookings)
- **Host Approves Booking** *(optional)* (Bookings)
- **Make Payment / Refund** (Payments)
- **View Booking History** (Bookings)
- **Leave Review** (Reviews)
- **Admin Moderation** (Admin)

---

## 4) Mermaid Diagram 

> Note: Mermaid doesn’t have a native “use-case” shape, so I approximate with circles for use cases and styled boxes for actors/domains.

```mermaid
flowchart LR
  %% ===== Styles =====
  classDef actor fill:#f6f8fa,stroke:#57606a,color:#24292f,stroke-width:1px;
  classDef usecase fill:#eef6ff,stroke:#0969da,color:#0a3069,stroke-width:1px;

  %% ===== Actors =====
  G["Guest"]:::actor
  U["Authenticated User / Guest"]:::actor
  H["Host"]:::actor
  A["Admin"]:::actor
  PG["Payment Gateway"]:::actor
  NS["Notification Service"]:::actor

  %% ===== Use Cases =====
  UC_Register(("Register / Login")):::usecase
  UC_Profile(("Manage Profile")):::usecase
  UC_Browse(("Browse Properties")):::usecase
  UC_CreateBooking(("Create Booking")):::usecase
  UC_CancelBooking(("Cancel Booking")):::usecase
  UC_MakePayment(("Make Payment")):::usecase
  UC_LeaveReview(("Leave Review")):::usecase
  UC_CreateProp(("Create / Manage Property")):::usecase
  UC_HostApprove(("Approve Booking")):::usecase
  UC_AdminModeration(("Admin Moderation")):::usecase

  %% ===== Actor to Use Case connections =====
  G --> UC_Register
  G --> UC_Browse

  U --> UC_Profile
  U --> UC_CreateBooking
  U --> UC_CancelBooking
  U --> UC_MakePayment
  U --> UC_LeaveReview

  H --> UC_CreateProp
  H --> UC_HostApprove

  A --> UC_AdminModeration

  %% ===== External Systems =====
  UC_MakePayment --> PG
  UC_CreateBooking --> NS
  UC_CancelBooking --> NS
```
