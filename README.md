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

## 4) Mermaid Diagram (renders on GitHub)

> Note: Mermaid doesn’t have a native “use-case” shape, so we approximate with circles for use cases and styled boxes for actors/domains.

```mermaid
flowchart LR
  %% ===== Styles =====
  classDef actor fill:#f6f8fa,stroke:#57606a,color:#24292f,stroke-width:1px;
  classDef system fill:#fff,stroke:#d0d7de,color:#24292f,stroke-width:1px;
  classDef usecase fill:#eef6ff,stroke:#0969da,color:#0a3069,stroke-width:1px;

  %% ===== Actors =====
  G[Guest]:::actor
  U[Authenticated User (Guest)]:::actor
  H[Host]:::actor
  A[Admin]:::actor
  PG[Payment Gateway]:::actor
  NS[Notification Service]:::actor

  %% ===== System Domains =====
  subgraph AUTH[Auth & Users]
    UC_Register(("Register")):::usecase
    UC_Login(("Login")):::usecase
    UC_Profile(("Manage Profile")):::usecase
  end

  subgraph LISTINGS[Listings]
    UC_Browse(("Browse/Search Properties")):::usecase
    UC_CreateProp(("Create Property")):::usecase
    UC_EditProp(("Edit/Delete Property")):::usecase
    UC_UploadImg(("Upload Images")):::usecase
    UC_Availability(("Manage Availability")):::usecase
  end

  subgraph BOOKINGS[Bookings]
    UC_CreateBooking(("Create Booking")):::usecase
    UC_CancelBooking(("Cancel Booking")):::usecase
    UC_HostApprove(("Host Approves Booking")):::usecase
    UC_History(("View Booking History")):::usecase
  end

  subgraph PAYMENTS[Payments]
    UC_MakePayment(("Make Payment")):::usecase
    UC_Refund(("Refund")):::usecase
  end

  subgraph REVIEWS[Reviews]
    UC_LeaveReview(("Leave Review")):::usecase
  end

  subgraph ADMIN[Admin]
    UC_AdminModeration(("Admin Moderation")):::usecase
  end

  %% ===== Actor -> Use Case edges =====
  G --> UC_Register
  G --> UC_Login
  G --> UC_Browse

  U --> UC_Profile
  U --> UC_CreateBooking
  U --> UC_CancelBooking
  U --> UC_MakePayment
  U --> UC_History
  U --> UC_LeaveReview

  H --> UC_CreateProp
  H --> UC_EditProp
  H --> UC_UploadImg
  H --> UC_Availability
  H --> UC_HostApprove
  H --> UC_CancelBooking

  A --> UC_AdminModeration

  %% ===== External Interactions =====
  UC_MakePayment --> PG
  UC_Refund --> PG
  UC_CreateBooking --> NS
  UC_CancelBooking --> NS
