# Backend Requirements Specifications

## ALX Airbnb Project Documentation

### Table of Contents

1. [Overview](#overview)
2. [User Authentication System](#user-authentication-system)
3. [Property Management System](#property-management-system)
4. [Booking System](#booking-system)
5. [Payment Processing System](#payment-processing-system)

---

## Overview

This document outlines the technical and functional requirements for the core backend features of the ALX Airbnb clone application. Each feature includes detailed API specifications, validation rules, and performance criteria to ensure robust and scalable implementation.

---

## User Authentication System

### 1.1 Functional Requirements

#### 1.1.1 User Registration

- **Description:** Allow new users to create accounts with email verification
- **User Types:** Guest, Host, Admin
- **Registration Methods:** Email/Password, Social Login (Google, Facebook)

#### 1.1.2 User Login

- **Description:** Authenticate existing users and manage sessions
- **Authentication Methods:** Email/Password, JWT tokens, OAuth2

#### 1.1.3 Profile Management

- **Description:** Enable users to update their profile information
- **Features:** Profile picture, personal details, preferences, verification status

### 1.2 API Endpoints

#### 1.2.1 User Registration

```http
POST /api/v1/auth/register
```

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1234567890",
  "role": "guest", // guest, host, admin
  "dateOfBirth": "1990-01-01",
  "termsAccepted": true
}
```

**Response (201 Created):**

```json
{
  "success": true,
  "message": "Registration successful. Please check your email for verification.",
  "data": {
    "userId": "uuid-string",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "role": "guest",
    "isVerified": false,
    "createdAt": "2025-07-10T10:30:00Z"
  }
}
```

**Error Response (400 Bad Request):**

```json
{
  "success": false,
  "error": "VALIDATION_ERROR",
  "message": "Invalid input data",
  "details": [
    {
      "field": "email",
      "message": "Email already exists"
    }
  ]
}
```

#### 1.2.2 User Login

```http
POST /api/v1/auth/login
```

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "userId": "uuid-string",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "guest",
      "isVerified": true
    },
    "tokens": {
      "accessToken": "jwt-access-token",
      "refreshToken": "jwt-refresh-token",
      "expiresIn": 3600
    }
  }
}
```

#### 1.2.3 Profile Management

```http
GET /api/v1/users/profile
PUT /api/v1/users/profile
```

**GET Response (200 OK):**

```json
{
  "success": true,
  "data": {
    "userId": "uuid-string",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890",
    "dateOfBirth": "1990-01-01",
    "profilePicture": "https://example.com/profile.jpg",
    "bio": "Travel enthusiast...",
    "isVerified": true,
    "createdAt": "2025-07-10T10:30:00Z",
    "updatedAt": "2025-07-10T12:00:00Z"
  }
}
```

### 1.3 Validation Rules

#### 1.3.1 Registration Validation

- **Email:** Valid email format, unique, max 255 characters
- **Password:** Min 8 characters, include uppercase, lowercase, number, special character
- **First Name:** Required, 2-50 characters, letters only
- **Last Name:** Required, 2-50 characters, letters only
- **Phone:** Valid international format, unique
- **Date of Birth:** User must be 18+ years old
- **Terms:** Must be accepted (true)

#### 1.3.2 Login Validation

- **Email:** Valid email format, required
- **Password:** Required, min 8 characters
- **Rate Limiting:** Max 5 failed attempts per IP per hour

### 1.4 Performance Criteria

- **Response Time:** < 200ms for login, < 500ms for registration
- **Concurrent Users:** Support 10,000 concurrent authentication requests
- **Token Expiry:** Access tokens expire in 1 hour, refresh tokens in 30 days
- **Database Queries:** Single query for user lookup, optimized with indexes

---

## Property Management System

### 2.1 Functional Requirements

#### 2.1.1 Property Listing

- **Description:** Allow hosts to create and manage property listings
- **Features:** Property details, photos, amenities, pricing, availability

#### 2.1.2 Property Search

- **Description:** Enable guests to search and filter properties
- **Features:** Location-based search, price range, amenities, availability dates

#### 2.1.3 Property Management

- **Description:** CRUD operations for property owners
- **Features:** Create, read, update, delete properties

### 2.2 API Endpoints

#### 2.2.1 Create Property

```http
POST /api/v1/properties
```

**Request Body:**

```json
{
  "title": "Beautiful Beachfront Villa",
  "description": "Stunning 3-bedroom villa with ocean views...",
  "propertyType": "villa", // apartment, house, villa, etc.
  "address": {
    "street": "123 Ocean Drive",
    "city": "Miami",
    "state": "Florida",
    "country": "USA",
    "zipCode": "33139",
    "coordinates": {
      "latitude": 25.7617,
      "longitude": -80.1918
    }
  },
  "specifications": {
    "bedrooms": 3,
    "bathrooms": 2,
    "maxGuests": 6,
    "area": 150,
    "areaUnit": "sqm"
  },
  "amenities": ["wifi", "pool", "parking", "kitchen", "air-conditioning"],
  "pricing": {
    "basePrice": 200.0,
    "currency": "USD",
    "cleaningFee": 50.0,
    "serviceFee": 30.0
  },
  "images": [
    "https://example.com/image1.jpg",
    "https://example.com/image2.jpg"
  ],
  "houseRules": {
    "checkIn": "15:00",
    "checkOut": "11:00",
    "smokingAllowed": false,
    "petsAllowed": true,
    "partiesAllowed": false
  },
  "isActive": true
}
```

**Response (201 Created):**

```json
{
  "success": true,
  "message": "Property created successfully",
  "data": {
    "propertyId": "uuid-string",
    "title": "Beautiful Beachfront Villa",
    "hostId": "uuid-string",
    "status": "pending", // pending, approved, rejected
    "createdAt": "2025-07-10T10:30:00Z",
    "slug": "beautiful-beachfront-villa-miami"
  }
}
```

#### 2.2.2 Search Properties

```http
GET /api/v1/properties/search
```

**Query Parameters:**

```
?location=Miami,FL
&checkIn=2025-08-01
&checkOut=2025-08-07
&guests=4
&minPrice=100
&maxPrice=500
&amenities=wifi,pool
&propertyType=villa
&page=1
&limit=20
&sortBy=price
&sortOrder=asc
```

**Response (200 OK):**

```json
{
  "success": true,
  "data": {
    "properties": [
      {
        "propertyId": "uuid-string",
        "title": "Beautiful Beachfront Villa",
        "location": "Miami, FL",
        "price": 200.0,
        "rating": 4.8,
        "reviewCount": 42,
        "images": ["https://example.com/image1.jpg"],
        "specifications": {
          "bedrooms": 3,
          "bathrooms": 2,
          "maxGuests": 6
        },
        "amenities": ["wifi", "pool", "parking"],
        "isInstantBook": true,
        "hostInfo": {
          "hostId": "uuid-string",
          "firstName": "Jane",
          "isSuperhost": true
        }
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 100,
      "itemsPerPage": 20
    },
    "filters": {
      "appliedFilters": {
        "location": "Miami,FL",
        "guests": 4,
        "priceRange": [100, 500]
      }
    }
  }
}
```

#### 2.2.3 Get Property Details

```http
GET /api/v1/properties/{propertyId}
```

**Response (200 OK):**

```json
{
  "success": true,
  "data": {
    "propertyId": "uuid-string",
    "title": "Beautiful Beachfront Villa",
    "description": "Stunning 3-bedroom villa...",
    "hostId": "uuid-string",
    "propertyType": "villa",
    "address": {
      "street": "123 Ocean Drive",
      "city": "Miami",
      "state": "Florida",
      "country": "USA",
      "zipCode": "33139",
      "coordinates": {
        "latitude": 25.7617,
        "longitude": -80.1918
      }
    },
    "specifications": {
      "bedrooms": 3,
      "bathrooms": 2,
      "maxGuests": 6,
      "area": 150,
      "areaUnit": "sqm"
    },
    "amenities": ["wifi", "pool", "parking", "kitchen"],
    "pricing": {
      "basePrice": 200.0,
      "currency": "USD",
      "cleaningFee": 50.0,
      "serviceFee": 30.0
    },
    "images": [
      "https://example.com/image1.jpg",
      "https://example.com/image2.jpg"
    ],
    "houseRules": {
      "checkIn": "15:00",
      "checkOut": "11:00",
      "smokingAllowed": false,
      "petsAllowed": true,
      "partiesAllowed": false
    },
    "availability": {
      "isInstantBook": true,
      "minimumStay": 2,
      "maximumStay": 30
    },
    "rating": 4.8,
    "reviewCount": 42,
    "hostInfo": {
      "hostId": "uuid-string",
      "firstName": "Jane",
      "lastName": "Smith",
      "joinDate": "2023-01-15",
      "isSuperhost": true,
      "responseRate": 95,
      "responseTime": "within an hour"
    },
    "isActive": true,
    "createdAt": "2025-07-10T10:30:00Z",
    "updatedAt": "2025-07-10T12:00:00Z"
  }
}
```

### 2.3 Validation Rules

#### 2.3.1 Property Creation Validation

- **Title:** Required, 10-100 characters
- **Description:** Required, 50-2000 characters
- **Address:** All fields required, valid coordinates
- **Specifications:** Positive integers, realistic values
- **Pricing:** Positive numbers, valid currency codes
- **Images:** Min 3 images, valid URLs, max 10MB each
- **Amenities:** From predefined list

#### 2.3.2 Search Validation

- **Dates:** Check-in < Check-out, future dates only
- **Guests:** Min 1, max 16 guests
- **Price Range:** Min ≤ Max, positive values
- **Pagination:** Page ≥ 1, limit 1-100

### 2.4 Performance Criteria

- **Search Response:** < 300ms for basic search
- **Property Details:** < 200ms response time
- **Image Loading:** Optimized with CDN, lazy loading
- **Database:** Indexed on location, price, availability
- **Caching:** Redis cache for popular searches (5-minute TTL)

---

## Booking System

### 3.1 Functional Requirements

#### 3.1.1 Booking Creation

- **Description:** Allow guests to book available properties
- **Features:** Date validation, pricing calculation, instant booking

#### 3.1.2 Booking Management

- **Description:** Enable hosts and guests to manage bookings
- **Features:** View, modify, cancel bookings

#### 3.1.3 Booking Status Tracking

- **Description:** Track booking lifecycle from creation to completion
- **States:** Pending, Confirmed, Cancelled, Completed

### 3.2 API Endpoints

#### 3.2.1 Create Booking

```http
POST /api/v1/bookings
```

**Request Body:**

```json
{
  "propertyId": "uuid-string",
  "checkInDate": "2025-08-01",
  "checkOutDate": "2025-08-07",
  "guests": {
    "adults": 2,
    "children": 1,
    "infants": 0
  },
  "guestDetails": {
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "phone": "+1234567890"
  },
  "specialRequests": "Late check-in requested",
  "paymentMethodId": "pm_123456789"
}
```

**Response (201 Created):**

```json
{
  "success": true,
  "message": "Booking created successfully",
  "data": {
    "bookingId": "uuid-string",
    "propertyId": "uuid-string",
    "guestId": "uuid-string",
    "hostId": "uuid-string",
    "status": "pending",
    "checkInDate": "2025-08-01",
    "checkOutDate": "2025-08-07",
    "nights": 6,
    "guests": {
      "adults": 2,
      "children": 1,
      "infants": 0,
      "total": 3
    },
    "pricing": {
      "basePrice": 200.0,
      "totalNights": 6,
      "subtotal": 1200.0,
      "cleaningFee": 50.0,
      "serviceFee": 75.0,
      "taxes": 102.5,
      "total": 1427.5,
      "currency": "USD"
    },
    "paymentStatus": "pending",
    "createdAt": "2025-07-10T10:30:00Z",
    "expiresAt": "2025-07-10T11:30:00Z"
  }
}
```

#### 3.2.2 Get Booking Details

```http
GET /api/v1/bookings/{bookingId}
```

**Response (200 OK):**

```json
{
  "success": true,
  "data": {
    "bookingId": "uuid-string",
    "propertyId": "uuid-string",
    "property": {
      "title": "Beautiful Beachfront Villa",
      "address": "123 Ocean Drive, Miami, FL",
      "images": ["https://example.com/image1.jpg"]
    },
    "guestId": "uuid-string",
    "guestDetails": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "+1234567890"
    },
    "hostId": "uuid-string",
    "hostDetails": {
      "firstName": "Jane",
      "lastName": "Smith",
      "email": "jane@example.com",
      "phone": "+1987654321"
    },
    "status": "confirmed",
    "checkInDate": "2025-08-01",
    "checkOutDate": "2025-08-07",
    "nights": 6,
    "guests": {
      "adults": 2,
      "children": 1,
      "infants": 0,
      "total": 3
    },
    "pricing": {
      "basePrice": 200.0,
      "totalNights": 6,
      "subtotal": 1200.0,
      "cleaningFee": 50.0,
      "serviceFee": 75.0,
      "taxes": 102.5,
      "total": 1427.5,
      "currency": "USD"
    },
    "paymentStatus": "paid",
    "specialRequests": "Late check-in requested",
    "cancellationPolicy": "moderate",
    "createdAt": "2025-07-10T10:30:00Z",
    "confirmedAt": "2025-07-10T10:45:00Z",
    "updatedAt": "2025-07-10T10:45:00Z"
  }
}
```

#### 3.2.3 Cancel Booking

```http
DELETE /api/v1/bookings/{bookingId}
```

**Request Body:**

```json
{
  "reason": "CHANGE_OF_PLANS",
  "comments": "Family emergency, need to cancel"
}
```

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Booking cancelled successfully",
  "data": {
    "bookingId": "uuid-string",
    "status": "cancelled",
    "cancellationReason": "CHANGE_OF_PLANS",
    "cancelledAt": "2025-07-10T14:00:00Z",
    "refund": {
      "amount": 1285.0,
      "currency": "USD",
      "processingTime": "3-5 business days",
      "refundId": "ref_123456789"
    }
  }
}
```

### 3.3 Validation Rules

#### 3.3.1 Booking Creation Validation

- **Property ID:** Must exist and be active
- **Dates:** Check-in < Check-out, future dates, available dates
- **Guests:** Within property limits, positive integers
- **Guest Details:** Valid email, phone, names required
- **Payment:** Valid payment method required

#### 3.3.2 Booking Modification Validation

- **Date Changes:** Min 24 hours before check-in
- **Guest Changes:** Within property limits
- **Cancellation:** Based on cancellation policy

### 3.4 Performance Criteria

- **Booking Creation:** < 1 second response time
- **Availability Check:** < 200ms response time
- **Concurrent Bookings:** Handle race conditions with database locks
- **Payment Processing:** < 5 seconds for payment confirmation

---

## Payment Processing System

### 4.1 Functional Requirements

#### 4.1.1 Payment Processing

- **Description:** Handle secure payment transactions
- **Features:** Credit card, PayPal, bank transfer support
- **Security:** PCI DSS compliance, fraud detection

#### 4.1.2 Refund Management

- **Description:** Process refunds based on cancellation policies
- **Features:** Full/partial refunds, automated processing

### 4.2 API Endpoints

#### 4.2.1 Process Payment

```http
POST /api/v1/payments/process
```

**Request Body:**

```json
{
  "bookingId": "uuid-string",
  "amount": 1427.5,
  "currency": "USD",
  "paymentMethod": {
    "type": "card",
    "cardToken": "tok_123456789"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY",
    "country": "USA",
    "zipCode": "10001"
  }
}
```

**Response (200 OK):**

```json
{
  "success": true,
  "message": "Payment processed successfully",
  "data": {
    "paymentId": "uuid-string",
    "bookingId": "uuid-string",
    "amount": 1427.5,
    "currency": "USD",
    "status": "succeeded",
    "paymentMethod": "card",
    "transactionId": "txn_123456789",
    "processedAt": "2025-07-10T10:30:00Z"
  }
}
```

### 4.3 Validation Rules

- **Amount:** Must match booking total
- **Currency:** Must match booking currency
- **Payment Method:** Valid and not expired
- **Billing Address:** Required for card payments

### 4.4 Performance Criteria

- **Payment Processing:** < 5 seconds response time
- **Fraud Detection:** < 1 second analysis
- **Refund Processing:** < 10 seconds for automated refunds
