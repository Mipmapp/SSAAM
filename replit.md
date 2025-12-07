# SSAAM - Student School Activities Attendance Monitoring

## Overview
A Vue 3 + Vite frontend application for monitoring student attendance at school activities. This is a Single Page Application (SPA) integrated with a backend API for user authentication and student data management.

## Project Structure
- **Framework**: Vue 3 with Composition API (script setup)
- **Build Tool**: Vite 5
- **Styling**: Tailwind CSS
- **Routing**: Vue Router 4
- **Backend API**: https://ssaam-api.vercel.app

## Key Features
- Dual-role authentication: Students (numeric IDs) and Masters/Teachers (letter-starting IDs)
- API-based student data management with Bearer token authentication
- Admin/Master dashboard with full CRUD operations on student records
- User statistics by program and year level (fetched separately from dedicated endpoint)
- Search/filter with pagination (10-20 students per page)
- RFID code support for attendance
- Professional SVG icon system throughout
- Help/Contact modal accessible from Login and Dashboard
- Mobile hamburger menu with smooth animations
- Advanced form validation with custom error messages
- Image management via ImgBB with 200KB compression and 3-attempt retry
- Team member photos in developer popup

## Configuration
- **Dev Server**: Port 5000, host 0.0.0.0
- **Vite Config**: Configured with `allowedHosts: true` for Replit proxy compatibility
- **API Base**: `https://ssaam-api.vercel.app`

## Backend Endpoints (Latest - 2025-11-30)

### Existing Endpoints
- **GET** `/apis/students` - Paginated list (10-20 per page)
- **POST** `/apis/students` - Create new student
- **PUT** `/apis/students/{student_id}` - Update student
- **DELETE** `/apis/students/{student_id}` - Delete student
- **POST** `/apis/students/login` - Student login
- **POST** `/apis/masters/login` - Master/admin login

### New Endpoints (Pagination Architecture - Latest)
- **GET** `/apis/students/stats` - Dashboard statistics (all students by program/year)
- **GET** `/apis/students/search` - Search/filter with pagination
  - Query params: `search`, `program`, `yearLevel`, `page`, `limit`
  - Returns paginated results only (10-20 per page)

## Pagination Architecture

### Why Pagination?
- Prevents lag with thousands of students in database
- Only loads 10-20 students per page for optimal performance
- Statistics fetched separately without pagination for complete data

### Frontend Logic
1. **Page Load**: Fetch page 1 (10 students) + fetch stats separately
2. **Search**: Call `/apis/students/search` with filters, reset to page 1
3. **Navigation**: Click Next/Previous to fetch other pages
4. **Filters**: Dropdown changes trigger new search query

### Backend Logic
- `/apis/students` - Returns only current page (limit 10-20)
- `/apis/students/stats` - Returns aggregated counts for all students
- `/apis/students/search` - Filters and returns paginated results

## Authentication & API Integration

### Login Flow
- **Students** (ID starts with number): POST `/apis/students/login` 
- **Masters/Teachers** (ID starts with letter): POST `/apis/masters/login`

### Admin Dashboard Features
- **Greeting**: Shows "Welcome back, Admin!" for both admin and master users
- **Manage Tab**: Access available to both admin and master users
- **Student Pagination**: Shows 10 students per page with Next/Previous buttons
- **Search/Filter**: 
  - Search by Name, Email, Student ID, RFID Code
  - Filter by Program (BSCS, BSIS, BSIT)
  - Filter by School Level (1st-4th year)
  - Pagination resets to page 1 on filter change
- **Statistics Dashboard**: Shows all students by program/year (fetched from `/apis/students/stats`)
- **Full CRUD**: Edit (name, email, program, year level) or delete students

### API Keys & Secrets
- **SSAAM_API_KEY**: Bearer token for master login (stored as Replit secret)
- **SSAAM_STUDENTS_API_KEY**: Bearer token for student operations (hardcoded: SSAAMStudents)

## Image Management
- **Compression**: Images compressed from 600KB to 200KB using progressive quality reduction
- **Upload Service**: ImgBB with random API key distribution
- **Retry Logic**: 3-attempt automatic retry if upload fails (1 second delay between attempts)
- **Supported Formats**: JPEG (optimized for size)

## Development Notes
- Form validation: Student ID format (`12-A-12345`), name validation (Unicode support: ñ, á, é, etc.)
- Error notifications styled in red for clear visibility
- Mobile responsive design with hamburger menu
- Cross-browser password field styling (hidden native controls, custom visibility toggle)
- Dashboard statistics show COMPLETE count of all students (not just current page)
- Pagination UI shows: "Page X of Y (Total: Z students)"

## Architecture
Frontend SPA with efficient pagination:
- Displays current page only (10-20 students)
- Fetches statistics separately for complete dashboard data
- Uses dedicated search endpoint for filtered results
- Backend aggregates all student data for statistics
- No client-side filtering - all search/filter done server-side

## Latest Updates (2025-12-07)

### Dashboard & Notifications Updates (LATEST - 2025-12-07)
- **Secure Image Upload Endpoint**: 
  - New `/apis/upload-image` endpoint handles ImgBB uploads securely
  - Uses `canPostNotification` middleware for consistent authentication
  - API keys remain server-side (IMGBB_API_KEYS env var in Vercel)
  - Frontend uploads image first, then posts notification with URL
  - Supports both admin and MedPub users
- **Link Highlighting in Announcements**:
  - URLs in announcement messages are automatically converted to clickable links
  - Links styled with purple-to-pink gradient matching SSAAM theme
  - Uses `formatMessageWithLinks()` function with HTML entity escaping for security
- **Dashboard Stats Fix**:
  - Backend `/apis/students/stats` now normalizes program and year_level values
  - Handles different casing and formats (e.g., "1ST YEAR", "1st Year", "FIRST YEAR")
  - Stats now correctly show counts for all students
- **Notification Date/Time Display**:
  - Shows created date/time formatted as "Dec 7, 2024 • 5:30 PM"
  - Shows "Updated" timestamp if notification was edited
- **Sidebar Scrollbar Styling**:
  - Custom scrollbar matching purple/pink gradient theme
  - Translucent white scrollbar thumb with hover effect
  - Works on both desktop and mobile sidebars

### Password Reset Feature
- **API Endpoints**: Three-step password reset flow via email verification
  - **POST** `/apis/password-reset/request` - Request reset code (sent to registered email)
  - **POST** `/apis/password-reset/verify` - Verify 6-digit code and get reset token
  - **POST** `/apis/password-reset/complete` - Complete password reset with token
- **Security Features**:
  - Rate limiting: 1-minute cooldown between requests, 15-minute lockout after 5 failed attempts
  - Hashed codes: Reset codes and tokens stored hashed in database
  - Enumeration prevention: Consistent response message whether account exists or not
  - Atomic operations: Uses findOneAndUpdate to prevent race conditions
  - Verification attempt tracking: Max 5 attempts per reset code before lockout
  - Two-step token flow: Must verify code before getting reset token
- **Email**: Uses Gmail SMTP with same credentials as RFID notification

### Notifications API (LATEST)
- **API Endpoints**: Notification system for students and admins
  - **GET** `/apis/notifications` - Get all notifications (requires authentication)
  - **POST** `/apis/notifications` - Create notification (admin or medpub only)
  - **PUT** `/apis/notifications/:id` - Update notification (owner or admin only)
  - **DELETE** `/apis/notifications/:id` - Delete notification (owner or admin only)
- **Security Features**:
  - JWT-based authentication via canPostNotification middleware
  - Role-based permissions: Only admin can set urgent priority
  - Ownership checks: Only poster or admin can edit/delete
  - MedPub restriction: Cannot delete admin notifications
  - Identity derived from token: Poster info comes from JWT, not request body
- **Notification Schema**:
  - Fields: title (max 200), message (max 2000), priority (normal/important/urgent)
  - Auto-tracking: posted_by, posted_by_name, posted_by_id, created_at, updated_at

### RFID Email Notification Feature
- **Email Notification**: When an admin verifies a student's RFID code, the student receives an email notification
  - Email includes: RFID code, who verified it, and the verification date
  - Uses Gmail SMTP via nodemailer with `pabbly.bot.2@gmail.com`
  - Requires `GMAIL_APP_PASSWORD` environment variable in Vercel
- **Enhanced Student Dashboard**: Students can now see detailed RFID verification status
  - Shows "Verified" (green) or "Pending" (yellow) status badge
  - When verified: Shows RFID code, verifier name, and date
  - When unverified: Shows instructions to visit CCS office
  - Notifies students they will receive email when verified
- **Backend Function**: `sendRFIDVerificationEmail(toEmail, studentName, rfidCode, verifiedBy)`
- **API Response Update**: PUT `/apis/students/:student_id/rfid` now includes `emailSent: boolean`

## Updates (2025-12-05)

### Admin-Only Edit/Delete & Uppercase Names
- **Uppercase Name Enforcement**: All student names (first, middle, last) are automatically converted to uppercase
  - Backend: Names are uppercased during registration and editing
  - Frontend: Names displayed and input as uppercase
- **Admin-Only Operations**: Edit and Delete student operations now require admin authentication
  - **PUT** `/apis/students/:student_id` - Now requires admin JWT token (was student token)
  - **DELETE** `/apis/students/:student_id` - Now requires admin JWT token (was student token)
  - Dashboard checks for admin role before allowing edit/delete operations
  - Non-admin users cannot edit or delete student records
- **Student ID Validation**: Only allows IDs from 21-A-XXXXX to 25-A-XXXXX
  - Frontend: Validates format and year range during registration
  - Backend: Additional validation layer for security
- **Updated Backend Reference**: `SSAAM_VERCEL_BACKEND_COMPLETE.js` includes all security updates

### Admin Settings Feature
- **Access Control Settings**: Admin/Master dashboard now includes a Settings page to control user access
  - **User Registration Toggle**: Enable/disable new student registration
  - **User Login Toggle**: Enable/disable student login (Masters can still log in)
  - **Custom Messages**: Display custom messages when features are disabled
- **Token Parameter Rename**: Changed `_ssaam_ts` to `_ssaam_access_token` for better clarity
- **MongoDB Settings Storage**: Settings stored in MongoDB with the following structure:
  ```javascript
  { 
    userRegister: { register: boolean, message: string }, 
    userLogin: { login: boolean, message: string } 
  }
  ```
- **New API Endpoints**:
  - **GET** `/apis/settings` - Retrieve current settings (public access)
  - **PUT** `/apis/settings` - Update settings (admin/master only with auth token)
- **Frontend Integration**:
  - Login.vue and Register.vue check settings on page load
  - Warning banners displayed when features are disabled
  - Form submission blocked with appropriate error messages
  - Master/Admin users bypass student login restrictions
- **Backend Integration**:
  - Student registration endpoint checks `userRegister.register` setting
  - Student login endpoint checks `userLogin.login` setting
  - Returns custom message from admin when feature is disabled

## Updates (2025-12-04)

### Security & Validation Enhancements
- **Encrypted Timestamp Authentication**: All protected API calls (POST, PUT, DELETE, login) now include an encrypted timestamp
  - Frontend: Uses `src/utils/ssaamCrypto.js` with XOR+Base64 encoding
  - Backend: Validates timestamp freshness (1-minute window) before processing requests
  - Clock skew tolerance: -30 seconds to +1 minute to handle minor time differences
  - No secret key needed - uses shared key `SSAAM2025CCS` on both ends
  - Requests with missing/expired timestamps are rejected with 401 Unauthorized
- **Anti-Bot Protection for Registration**:
  - User-Agent validation: Blocks requests without valid browser User-Agent
  - Bot pattern detection: Blocks curl, wget, python-requests, postman, insomnia, httpie, and common bot crawlers
  - Rate limiting: 1-minute cooldown per IP + student_id combination
  - Returns 429 (Too Many Requests) with countdown for rate-limited attempts
- **Student ID Validation**: Registration now only allows student IDs from 21-A-XXXXX to 25-A-XXXXX
  - Frontend validation with user-friendly error messages
  - Backend validation as additional security layer
- **Updated Backend**: `SSAAM_VERCEL_BACKEND_COMPLETE.js` includes `timestampAuth` and `antiBotProtection` middleware

## Updates (2025-11-30)

### Pagination & Statistics Separation
- **New Endpoint**: `/apis/students/stats` for dashboard statistics
- **New Endpoint**: `/apis/students/search` for search/filter with pagination
- **Frontend Changes**: 
  - Only fetches current page (10 students) on load
  - Fetches statistics from separate endpoint
  - Search/filter uses dedicated search endpoint
  - Pagination buttons: Next/Previous to navigate pages
  - All filtering done server-side for performance
- **Backend**: `SSAAM_VERCEL_BACKEND_COMPLETE.js` - Complete ready-to-deploy code
  - Copy and paste into your Vercel backend
  - Includes all endpoints (existing + 2 new)
  - Ready for immediate deployment
- **Performance**: No longer loads all pages - reduces load time and database queries
- **Image Compression**: 200KB target with 3-attempt retry on upload failure
