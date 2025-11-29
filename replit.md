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
- User statistics by program and year level
- RFID code support for attendance
- Professional SVG icon system throughout
- Help/Contact modal accessible from Login and Dashboard
- Mobile hamburger menu with smooth animations
- Advanced form validation with custom error messages
- Image management via ImgBB
- Team member photos in developer popup

## Configuration
- **Dev Server**: Port 5000, host 0.0.0.0
- **Vite Config**: Configured with `allowedHosts: true` for Replit proxy compatibility
- **API Base**: `https://ssaam-api.vercel.app`

## Authentication & API Integration (Latest - 2025-11-29)

### Login Flow
- **Students** (ID starts with number): POST `/apis/masters/login` returns JWT token
  - Used with `SSAAM_API_KEY` Bearer token for API access
- **Masters/Teachers** (ID starts with letter): POST `/apis/masters/login` returns JWT token
  - Same as students, used for admin dashboard access
  
### Admin Dashboard Features
- **Greeting**: Shows "Welcome back, Admin!" for both admin and master users
- **Manage Tab**: Access available to both admin and master users
- **Student Fetching**: 
  - GET `/apis/students` with Bearer token `SSAAM_STUDENTS_API_KEY`
  - Fetches and displays all registered students in table format
- **Student Operations**:
  - **GET** `/apis/students` - Fetch all students with authorization
  - **PUT** `/apis/students/{student_id}` - Update student details (name, email, program, year level)
  - **DELETE** `/apis/students/{student_id}` - Remove student account
- **Statistics Dashboard**: Shows student count by program (BSCS, BSIS, BSIT) and year level (1st-4th year)

### API Keys & Secrets
- **SSAAM_API_KEY**: Bearer token for master login and operations (stored as Replit secret)
- **VITE_SSAAM_STUDENTS_API_KEY**: Bearer token for fetching/managing students (set as environment variable)
  - Value: `SSAAMStudents`
  - Used in Authorization header: `Bearer SSAAMStudents`

### Database Integration
- All student data fetched from backend API
- Changes (edit/delete) immediately reflected in admin dashboard
- No localStorage dependency for admin data

## Development Notes
- Form validation includes Student ID format (`12-A-12345`), name validation (letters only)
- Error notifications styled in red for clear visibility
- Mobile responsive design with hamburger menu
- Cross-browser password field styling (hidden native controls, custom visibility toggle)
- Image upload support for student profiles via ImgBB API

## Architecture
Frontend application with full API integration for authentication and student data management. Uses Bearer token authentication for secure API access. Admin/Master accounts have full CRUD capabilities over student records through the API.

## Latest Updates (2025-11-29)

### Master Login & Admin Dashboard (LATEST)
- **Masters API Integration**: Login endpoint changed to POST `/apis/masters/login` with username and password
- **Admin Access**: Masters can now access admin dashboard with "Manage" menu option
- **Welcome Message**: Shows "Welcome back, Admin!" for admin and master users
- **Student Data Fetching**: Admin/Master dashboard fetches all students from API with `SSAAM_STUDENTS_API_KEY` Bearer token
- **Dashboard Statistics**: Displays registered students count by program and year level
- **Full CRUD**: Admin/Masters can view, edit (name, email, program, year level), and delete student accounts
- **Search Filter**: Advanced search with dropdown to filter by Student ID, Name, Email, RFID, Program, School Level
