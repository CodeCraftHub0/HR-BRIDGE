# Ascent Employee Bridge

## Project Overview
Ascent Employee Bridge is a comprehensive HR management platform designed to streamline employee, team leader, and admin workflows. It provides robust tools for task management, employee progress tracking, course enrollments, notifications, and administrative controls, all with a modern, user-friendly interface.

## Key Features
- **Role-based Dashboard:** Separate dashboards for Admin, Team Leader, and Employee roles.
- **Task Management:** Assign, update, and track tasks with progress and documentation.
- **Progress Tracking:** Employees and team leaders can view individual and departmental progress.
- **The Course Management:** Enroll in and track progress on department-specific courses.
- **Notifications:** System-wide and targeted notifications for important events.
- **User Management:** Admins can add, edit, promote, demote, and delete users.
- **PDF Export:** Export employee and team leader tables, as well as department tasks, to PDF.
- **Authentication & Authorization:** Secure JWT-based login and role-based access control.
- **Responsive UI:** Modern, mobile-friendly design using React and Tailwind CSS.

## Technologies Used
- **Frontend:** React, TypeScript, Tailwind CSS
- **Backend:** Python, Flask, MySQL, fpdf (for PDF generation)
- **Other:** JWT for authentication, axios/fetch for API calls

## Setup & Installation

### Prerequisites
- Node.js **16+**
- Python **3.9+**
- MySQL **8+**
- Git

### 1. Clone the project
```bash
git clone https://github.com/Yvantrey/HR-Project.git
cd HR-Project
```

### 2. Backend setup (Flask + MySQL)
1. **Create virtual environment (recommended)**
   ```bash
   cd Backend
   python -m venv .venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   ```
2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```
3. **Configure environment variables**
   - Duplicate `.env.example` as `.env`
   - Fill in:
     ```env
     DB_HOST=localhost
     DB_PORT=3306
     DB_USER=root
     DB_PASSWORD=your_password
     DB_NAME=hrdatabase
     SECRET_KEY=your_flask_secret
     JWT_SECRET=your_jwt_secret
     ALLOWED_ORIGINS=http://localhost:5173
     ```
4. **Create and seed the database**
   ```bash
   mysql -u root -p < ../src/database/realschema.sql
   ```
5. **Run the backend locally**
   ```bash
   python server.py
   ```
   The API listens on `http://127.0.0.1:5000`.

### 3. Frontend setup (Vite + React)
1. Install packages:
   ```bash
   cd ../
   npm install
   ```
2. **Environment variables**
   - Create `.env.local` in the project root:
     ```
     VITE_API_URL=http://127.0.0.1:5000/api
     ```
3. **Start the dev server**
   ```bash
   npm run dev
   ```
   Vite defaults to `http://localhost:5173`.

### 4. Deploying the backend on PythonAnywhere
1. Upload/clone the repo to `/home/<username>/HR-Project`.
2. Create a PythonAnywhere MySQL database (Databases tab).
3. Update the WSGI file (`/var/www/<app>.wsgi`) to set environment variables:
   ```python
   import os, sys
   os.environ['ALLOWED_ORIGINS'] = 'https://your-frontend.vercel.app'
   os.environ['DB_HOST'] = '<username>.mysql.pythonanywhere-services.com'
   os.environ['DB_USER'] = '<username>'
   os.environ['DB_PASSWORD'] = '<mysql_password>'
   os.environ['DB_NAME'] = '<username>$default'
   os.environ['DB_PORT'] = '3306'

   path = '/home/<username>/HR-Project'
   if path not in sys.path:
       sys.path.append(path)

   from Backend.server import app as application
   ```
4. Install requirements inside your PythonAnywhere virtualenv and run migrations:
   ```bash
   workon hr-project-env
   pip install -r Backend/requirements.txt
   mysql -u <username> -p'<mysql_password>' < src/database/realschema.sql
   ```
5. Reload the web app from the PythonAnywhere dashboard.

### 5. Frontend production deploy (Vercel)
1. Connect the GitHub repository on Vercel.
2. Set **Environment Variables** under *Settings → Environment Variables*:
   ```
   VITE_API_URL=https://yvantrey.pythonanywhere.com/api
   ```
3. Deploy. After deployment, verify login using the demo credentials in `LoginPage.tsx`.

### 6. Verifying the installation
1. Visit the frontend (local or Vercel).
2. Use the admin demo credentials:
   ```
   Email: admin@gmail.com
   Password: TestPassword123!
   ```
3. Confirm dashboards load and API calls hit the deployed backend (check browser dev tools → Network).

## Main API Endpoints
- **Authentication:**
  - `POST /api/auth/login` — User login
  - `GET /api/auth/validate` — Validate JWT token
- **User Management:**
  - `GET /api/users` — List users (admin/team leader)
  - `POST /api/users` — Create user (admin)
  - `PUT /api/users/<id>` — Update user
  - `DELETE /api/users/<id>` — Delete user
  - `POST /api/admin/export-employees-pdf` — Export employees as PDF (admin)
  - `POST /api/admin/export-team-leaders-pdf` — Export team leaders as PDF (admin)
- **Task Management:**
  - `GET /api/tasks/status` — Get tasks by status
  - `POST /api/tasks` — Create task
  - `PUT /api/tasks/<id>` — Update task
  - `PUT /api/tasks/<id>/progress` — Update task progress
  - `GET /api/team-leader/department-members-progress` — Get progress for all employees in department
  - `GET /api/team-leader/export-tasks-pdf` — Export department tasks as PDF (team leader)
- **Course Management:**
  - `GET /api/courses` — List courses
  - `GET /api/courses/watch-history/<course_id>` — Get course watch history
- **Notifications:**
  - `GET /api/notifications` — List notifications
  - `POST /api/notifications` — Create notification

## Usage Notes
- **Roles:**
  - Admin: Full access to all features and user management.
  - Team Leader: Manage department tasks and view team progress.
  - Employee: View and update own tasks and courses.
- **PDF Export:**
  - Employees, team leaders, and department tasks can be exported to PDF from the respective management pages.
- **Security:**
  - All sensitive endpoints require a valid JWT token in the `Authorization` header.

