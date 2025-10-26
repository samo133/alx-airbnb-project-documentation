# Airbnb Clone — User Stories

## 1) Authentication & User Management

**Story 1: Account Registration**
> As a new user, I want to register an account so that I can book stays or list my own properties.

**Story 2: Login & Profile Management**
> As a returning user, I want to log into my account and manage my personal information so that I can update my profile securely.

---

## 2) Property Management (Host)

**Story 3: Property Listing**
> As a host, I want to add new properties with details and images so that guests can view and book them.

**Story 4: Manage Listings**
> As a host, I want to edit or remove my listings so that my property information stays accurate and up to date.

---

## 3) Booking System

**Story 5: Create Booking**
> As a guest, I want to book a property for specific dates so that I can reserve a stay.

**Story 6: Manage Bookings**
> As a host, I want to approve or reject booking requests so that I can control my property availability.

**Story 7: View Booking History**
> As a user, I want to see all my past and upcoming bookings so that I can keep track of my travel plans.

---

## 4) Payments

**Story 8: Make Payment**
> As a guest, I want to pay securely for my booking using a credit card or wallet so that I can confirm my reservation.

**Story 9: Handle Payment Failures**
> As a user, I want to retry a failed payment so that I can complete my booking without redoing the entire process.

---

## 5) Reviews

**Story 10: Leave Review**
> As a guest, I want to leave a review and rating for the property after my stay so that I can share my experience with others.

---

## 6) Administration

**Story 11: Admin Moderation**
> As an admin, I want to manage users, listings, and reviews so that I can maintain a safe and reliable platform.

---

## 7) Optional / Future Enhancements

**Story 12: Notifications**
> As a user, I want to receive email notifications for booking confirmations, cancellations, and payments so that I stay informed.

**Story 13: Search & Filter**
> As a guest, I want to search for properties by location, price range, and amenities so that I can find options that match my preferences.

---

### ✅ Summary
| Domain | Example Stories | Purpose |
|--------|----------------|----------|
| Authentication | Register, Login | Access control |
| Property Management | List, Manage | Enable hosting |
| Booking | Book, Approve | Handle reservations |
| Payments | Pay, Retry | Secure transactions |
| Reviews | Leave Review | Build trust |
| Admin | Moderate | Platform integrity |

---

### ✅ Acceptance Criteria Example (Story 5)
**As a guest, I want to book a property for specific dates so that I can reserve a stay.**

**Acceptance Criteria:**
- User must be authenticated.  
- Booking fails if dates overlap existing bookings.  
- System returns booking ID and confirmation status.  
- Email notification sent to host and guest.

---

