# StayEase — PG Management & Room Booking Platform

## Overview

StayEase is a full-stack PG (Paying Guest) Management and Room Booking platform developed using a modern web stack. The platform allows users to browse available rooms, create accounts, book rooms, complete payments through Stripe, manage bookings, and interact with an admin-controlled room management system.

The application follows a frontend-backend architecture where:

* The frontend is built using HTML, CSS, Tailwind CSS, and Vanilla JavaScript.
* The backend is built using Node.js, Express.js, and TypeScript.
* PostgreSQL database hosting is provided using Supabase.
* Cloudinary is used for cloud-based image storage.
* Stripe is used for payment processing.
* Nodemailer is used for OTP email verification.

---

# Features

## User Features

* User Registration
* User Login
* OTP Verification
* Password Reset via OTP
* Browse Available Rooms
* Search and Filter Rooms
* Book Rooms
* Stripe Payment Integration
* Booking Confirmation
* Cancel Pending Bookings
* Save Interested Rooms
* View Personal Bookings

## Admin Features

* Admin Login
* Add Rooms
* Update Existing Rooms
* Delete Rooms
* Upload Room Images
* View Available Rooms
* Manage Room Availability

---

# Technologies Used

| Category               | Technology              |
| ---------------------- | ----------------------- |
| Frontend               | HTML5                   |
| Frontend Styling       | Tailwind CSS            |
| Frontend Logic         | Vanilla JavaScript      |
| Backend Runtime        | Node.js                 |
| Backend Framework      | Express.js              |
| Backend Language       | TypeScript              |
| Database               | PostgreSQL              |
| Database Hosting       | Supabase                |
| Image Hosting          | Cloudinary              |
| Payment Gateway        | Stripe                  |
| Email Service          | Nodemailer + Gmail SMTP |
| File Upload Middleware | Multer                  |
| Environment Variables  | dotenv                  |

---

# Project Architecture

```text
Frontend (HTML/CSS/JS)
        ↓
Express REST APIs
        ↓
TypeScript Backend Logic
        ↓
PostgreSQL Queries (pg package)
        ↓
Supabase PostgreSQL Database
```

Additional integrations:

```text
Image Upload → Cloudinary
Payments → Stripe
Emails/OTP → Nodemailer
```

---

# Folder Structure

```text
project-root/
│
├── public/
│   ├── index.html
│   ├── rooms.html
│   ├── book.html
│   ├── success.html
│   ├── dashboard.html
│   ├── admin.html
│   ├── login.html
│   ├── signup.html
│   └── js/
│       ├── api.js
│       ├── rooms.js
│       ├── admin.js
│       ├── book.js
│       ├── success.js
│       └── components.js
│
├── server.ts
├── package.json
├── tsconfig.json
├── .env
└── README.md
```

---

# Database Design

The project uses PostgreSQL through Supabase.

## Tables Used

### 1. users Table

Stores registered users.

| Column   | Type   | Description     |
| -------- | ------ | --------------- |
| id       | SERIAL | Primary Key     |
| name     | TEXT   | User Full Name  |
| email    | TEXT   | Unique Email    |
| password | TEXT   | User Password   |
| role     | TEXT   | user/admin role |

---

### 2. rooms Table

Stores PG room details.

| Column      | Type    | Description          |
| ----------- | ------- | -------------------- |
| id          | SERIAL  | Primary Key          |
| type        | TEXT    | Room Type            |
| price       | INTEGER | Monthly Price        |
| capacity    | INTEGER | Sharing Capacity     |
| available   | INTEGER | Available Rooms      |
| description | TEXT    | Room Description     |
| image       | TEXT    | Cloudinary Image URL |

---

### 3. bookings Table

Stores room booking information.

| Column      | Type    | Description          |
| ----------- | ------- | -------------------- |
| id          | SERIAL  | Primary Key          |
| room_id     | INTEGER | Linked Room ID       |
| user_name   | TEXT    | Customer Name        |
| user_email  | TEXT    | Customer Email       |
| user_phone  | TEXT    | Customer Phone       |
| check_in    | TEXT    | Check-in Date        |
| check_out   | TEXT    | Check-out Date       |
| services    | TEXT    | Extra Services       |
| total_price | INTEGER | Total Booking Amount |
| status      | TEXT    | pending/confirmed    |

---

### 4. otps Table

Stores OTP verification data.

| Column     | Type      | Description     |
| ---------- | --------- | --------------- |
| id         | SERIAL    | Primary Key     |
| email      | TEXT      | User Email      |
| otp        | TEXT      | Generated OTP   |
| expires_at | TIMESTAMP | OTP Expiry Time |

---

# API Endpoints

## Authentication APIs

| Method | Endpoint                    | Purpose                |
| ------ | --------------------------- | ---------------------- |
| POST   | /api/signup                 | Register User          |
| POST   | /api/login                  | User Login             |
| POST   | /api/otp/send               | Send OTP               |
| POST   | /api/password/reset-request | Password Reset OTP     |
| POST   | /api/password/reset-confirm | Confirm Password Reset |

---

## Room APIs

| Method | Endpoint             | Purpose     |
| ------ | -------------------- | ----------- |
| GET    | /api/rooms           | Fetch Rooms |
| POST   | /api/admin/rooms     | Add Room    |
| PUT    | /api/admin/rooms/:id | Update Room |
| DELETE | /api/admin/rooms/:id | Delete Room |

---

## Booking APIs

| Method | Endpoint                     | Purpose               |
| ------ | ---------------------------- | --------------------- |
| POST   | /api/book                    | Create Booking        |
| POST   | /api/create-checkout-session | Create Stripe Session |
| POST   | /api/confirm-booking         | Confirm Booking       |
| DELETE | /api/bookings/:id            | Cancel Booking        |
| GET    | /api/user/bookings           | Get User Bookings     |

---

## Upload APIs

| Method | Endpoint          | Purpose           |
| ------ | ----------------- | ----------------- |
| POST   | /api/admin/upload | Upload Room Image |

---

# Authentication System

The project currently uses a localStorage-based authentication mechanism.

## Current Authentication Flow

```text
User Login
    ↓
Backend verifies credentials
    ↓
Frontend stores user data in localStorage
    ↓
Protected pages check localStorage
```

## Admin Authentication

Admin access is verified using:

```javascript
localStorage.getItem('isAdmin') === 'true'
```

## Future Improvements

The system can later be upgraded using:

* JWT Authentication
* Session Management
* Role-Based Middleware
* Refresh Tokens
* Secure Password Hashing

---

# Stripe Payment Flow

The project integrates Stripe Checkout for secure online payments.

## Payment Workflow

```text
User clicks Book Now
        ↓
Booking stored with pending status
        ↓
Stripe Checkout Session Created
        ↓
User redirected to Stripe Payment Page
        ↓
Payment Success
        ↓
Booking status updated to confirmed
```

## Important Design Choice

Bookings are first stored with:

```text
status = pending
```

Only after successful payment:

```text
status = confirmed
```

This prevents invalid or unpaid bookings.

---

# Image Upload System

The application supports image uploads using Multer and Cloudinary.

## Upload Workflow

```text
Admin selects image
        ↓
Multer middleware processes file
        ↓
Cloudinary uploads image
        ↓
Cloudinary URL returned
        ↓
Image URL stored in PostgreSQL
```

## Why Cloudinary?

Cloudinary provides:

* Cloud image hosting
* CDN optimization
* Scalable storage
* Faster image delivery
* Reduced backend storage load

---

# Middleware Used

| Middleware        | Purpose                 |
| ----------------- | ----------------------- |
| express.json()    | Parse JSON request body |
| express.static()  | Serve frontend files    |
| multer            | Handle image uploads    |
| custom middleware | Dynamic HTML routing    |

---

# SQL Queries Used

The project uses raw SQL queries through the `pg` package.

## Query Types Used

| Query Type   | Purpose                |
| ------------ | ---------------------- |
| CREATE TABLE | Create database tables |
| INSERT       | Add new records        |
| SELECT       | Retrieve data          |
| UPDATE       | Modify records         |
| DELETE       | Remove records         |

## Example Query

```sql
SELECT * FROM rooms ORDER BY id ASC;
```

---

# Connection Pooling

The application uses PostgreSQL connection pooling.

```typescript
const pool = new Pool({
  connectionString: process.env.DATABASE_URL
});
```

Benefits:

* Reuses database connections
* Improves performance
* Handles multiple users efficiently
* Reduces server overhead

---

# Environment Variables

Example `.env` configuration:

```env
DATABASE_URL=
STRIPE_SECRET_KEY=
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
EMAIL_USER=
EMAIL_PASS=
APP_URL=
```

---

# Installation Steps

## Clone Repository

```bash
git clone <repository-url>
```

## Install Dependencies

```bash
npm install
```

## Run Development Server

```bash
npm run dev
```

---

# NPM Packages Used

| Package    | Purpose                |
| ---------- | ---------------------- |
| express    | Backend Framework      |
| pg         | PostgreSQL Connection  |
| multer     | File Upload Middleware |
| stripe     | Payment Integration    |
| cloudinary | Image Hosting          |
| dotenv     | Environment Variables  |
| nodemailer | Email Sending          |
| typescript | TypeScript Support     |
| nodemon    | Auto Restart           |
| ts-node    | Execute TypeScript     |

---

# Frontend Functionalities

## rooms.js

Handles:

* Room Fetching
* Room Rendering
* Search & Filtering
* Booking Navigation
* Interest Saving

## admin.js

Handles:

* Admin Room Management
* Add Room
* Edit Room
* Delete Room
* Image Upload

## book.js

Handles:

* Booking Form
* Price Calculation
* Booking API Calls
* Stripe Redirect

## success.js

Handles:

* Booking Confirmation
* Status Update
* Success/Error UI

---

# Security Features

Implemented:

* SQL Parameterized Queries
* Environment Variables
* OTP Verification
* Cloud Storage for Images
* Booking Status Validation

Potential Improvements:

* JWT Authentication
* bcrypt Password Hashing
* Stripe Webhooks
* CSRF Protection
* Rate Limiting

---

# Current Limitations

* Passwords are currently stored as plain text
* JWT authentication is not implemented yet
* Stripe webhook verification not implemented
* Admin role stored in localStorage

---

# Future Enhancements

* JWT Authentication
* Google OAuth Login
* Room Availability Calendar
* Real-time Notifications
* Mobile App Version
* Admin Analytics Dashboard
* Booking Invoice Generation
* Email Booking Confirmation
* AI-based Room Recommendation

---

# Learning Outcomes

This project helped in understanding:

* Full-stack web development
* REST API design
* PostgreSQL database management
* Express.js backend development
* TypeScript backend architecture
* Stripe payment integration
* File upload handling
* Cloudinary integration
* Frontend-backend communication
* Authentication systems
* Middleware usage

---

# Conclusion

StayEase is a scalable and modular PG management platform that demonstrates modern full-stack web development practices using Node.js, Express, PostgreSQL, Supabase, Stripe, Cloudinary, and Vanilla JavaScript.

The project successfully implements:

* User authentication
* Booking management
* Online payments
* Image hosting
* Admin panel
* Dynamic room management
* Database-driven architecture

This project serves as a strong demonstration of backend API development, frontend integration, SQL database handling, middleware implementation, and third-party service integration.
