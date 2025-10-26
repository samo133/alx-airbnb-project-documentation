# Flowchart — Property Booking & Payment

This chart maps the core backend workflow from “Select Dates” → “Confirmed Booking”, including payment intent and webhook confirmation.

```mermaid
flowchart TD
  %% Styles
  classDef step fill:#eef6ff,stroke:#0969da,color:#0a3069,stroke-width:1px;
  classDef io fill:#fff7e6,stroke:#d97706,color:#7c2d12,stroke-width:1px;
  classDef dec fill:#f6f8fa,stroke:#57606a,color:#24292f,stroke-width:1px;

  U[[User (Guest)]]:::io --> A[Authenticate (JWT/Session)]:::step
  A --> B[Select Property + Dates]:::step
  B --> C{Dates valid?}:::dec
  C -- No --> C1[Return 400: invalid date range]:::step --> Z[End]
  C -- Yes --> D[Check overlap in DB (no-conflict)]:::step
  D --> E{Available?}:::dec
  E -- No --> E1[Return 409: dates unavailable]:::step --> Z
  E -- Yes --> F[Create Booking: status=pending]:::step
  F --> G[Create Payment Intent (Stripe/PayPal)]:::step
  G --> H{Payment client confirm?}:::dec
  H -- Failed / Abandoned --> H1[Keep status=pending; allow retry or cancel]:::step --> Z
  H -- Succeeded --> I[Gateway sends webhook → Payments Service]:::step
  I --> J{Verify signature & intent state}:::dec
  J -- Invalid --> J1[Log + ignore or mark failed]:::step --> Z
  J -- Valid (succeeded) --> K[Mark Booking: status=confirmed]:::step
  K --> L[Record Payment (amount, currency, ref)]:::step
  L --> M[Invalidate cache: property availability]:::step
  M --> N[Notify guest & host (email/webhook)]:::step
  N --> O[Return 200 + booking_id + status=confirmed]:::step --> Z
```
