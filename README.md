# Milestone--2
# Athlete Performance Hub — Milestone 2

The **Athlete Performance Hub** is a biomechanical injury prevention and training load management portal designed for athletes, coaches, and physiotherapists. It integrates real-time computer vision pose estimation, training load metrics, injury historical logging, and professional reporting export options.

---

## Key Features in Milestone 2

### 1. Biomechanical Video Analysis & Kinematics Overlays
* **Real-time Overlays**: Renders skeletal joint connections dynamically on a canvas over the video player using browser-based tracking frames.
* **Kinematic Metrics Grid**: Computes and displays crucial motion analysis points:
  * **Min Knee Angle Flexion Depth**: Indicates squat/athletic posture depth.
  * **Bilateral Symmetry Index (BSI)**: Measures joint alignment symmetry.
  * **Flexion Velocity**: Calculates change of joint angles over time.
  * **Posture Alignment Score**: Validates torso/spine alignment.
* **Auto-Polling Mechanism**: Auto-polls the processing status every 3 seconds for newly uploaded video clips until analysis is complete.
* **Interactive Player Actions**: Added a `▶ Play & Analyze` button on every uploaded clip to launch playback and analytics overlays instantly.

### 🗑 2. Video Deletion & Graceful Orphaned File Cleanup
* **Permissions Control**: Athletes can delete their own videos, while coaches/physiotherapists/admins can delete any video records in the registry.
* **Purge Operations**: Deletion permanently deletes:
  * The database entry.
  * The raw uploaded MP4 file and the `processed_` MediaPipe overlay.
  * Any generated analysis frames and metadata.
* **Graceful Missing-File Handler**: If the database record exists but physical files are missing (orphaned records), the system cleans up the database entry without throwing a 500 server crash.
* **Instant React UI Sync**: Filters out the deleted video immediately from the UI state without a page refresh, and auto-selects the next available video (or shows a placeholder).

###  3. Sized Training Load Form & RPE Alignment
* **Overflow Protection**: Resolved styling issues where the RPE Rating (1–10) dropdown broke out of form containers on narrow layouts.
* **Responsive Layouts**: Designed form fields using CSS Grid (`.form-row` and `.form-group`) that automatically collapse to a single column on mobile screen sizes (< 600px).
* **Dropdown Option Legibility**: Styled dropdown `<option>` tags (`#12161f`) to provide maximum readability against dark theme backgrounds.

###  4. Standardized Injury Module (Injury Records)
* **Standardized Terminology**: Replaced all obsolete Phase 1 references to "Trauma Logs" with professional, user-friendly labels (**Injury Records**).
* **Injury Log Grid**: Tracks Injury Type, Affected Body Part, Severity (Low, Medium, High), Recovery Status (Active, Rehab, Recovered), and clinical assessment notes.

###  5. Simplified Auth & Instant Login
* **Frictionless Signup**: Newly created accounts default to `is_verified = True` for instant login capability without requiring manual token database validation.
* **Password Validation**: Relaxed constraints to a standard minimum of 6 characters, eliminating complex regex validation blocks while signing up.
* **CORS Wildcard Mapping**: Confirmed cross-origin requests succeed on any local development port (`3000`, `5173`, `5174`, etc.).

---

##  Clean Workspace Directory Structure

```text
C:\Users\neela\OneDrive\Documents\code
├── backend/
│   ├── app/
│   │   ├── routers/       (admin, athlete, auth, dataset, injury, report, training, video)
│   │   ├── services/      (analytics, pose_estimator, report_generator)
│   │   ├── database.py    (SQLAlchemy connection & schema migration module)
│   │   ├── main.py        (FastAPI setup & CORS configuration)
│   │   ├── models.py      (Database models)
│   │   └── schemas.py     (Pydantic schemas)
│   ├── uploads/           (Shared MP4 uploads and overlay files)
│   ├── athlete_hub.db     (Active SQLite database - 139 KB)
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── contexts/      (AuthContext.jsx)
│   │   ├── pages/         (Dashboard.jsx, Datasets.jsx, LoginRegister.jsx, VideoUpload.jsx)
│   │   ├── services/      (api.js - Axios config with Silent 401 Interceptors)
│   │   ├── App.jsx        (Routing matrix)
│   │   └── index.css      (Unified styling sheet)
│   ├── Dockerfile
│   └── nginx.conf         (Reverse proxy routing server)
├── deploy_aws.md          (AWS ECS/Fargate deployment guide)
├── docker-compose.yml     (Multi-container deployment config)
└── verify_hub.py          (280+ lines active Integration Test Suite)

🛠 Active Database Schema & Auto-Migrations
The project uses SQLAlchemy ORM to manage data. When the FastAPI application starts, the migrate_db() helper inside backend/app/database.py automatically checks for missing columns and appends them to the active database (athlete_hub.db) using ALTER TABLE statements:

Users Table: email, full_name, role, hashed_password, is_verified, verification_token, reset_token, refresh_token, last_login.
Athletes Table: date_of_birth, height_cm, weight_kg, sport, bio.
Videos Table: title, description, file_path, status, dataset_source, uploaded_at, skeletal_data (JSON frames), movement_score, analysis_summary.
📡 API Endpoints
Authentication
POST /api/auth/register - Create user profile (default is_verified=True, password min 6 chars)
POST /api/auth/login - OAuth2 login yielding JWT token
POST /api/auth/verify - Confirm user verification status
POST /api/auth/forgot-password - Request a password recovery token
POST /api/auth/reset-password - Execute password reset
Athletes & Profiles
GET /api/athletes/profile - Fetch current user athlete profile
PUT /api/athletes/profile - Update athlete statistics (height, weight, sport, bio)
GET /api/athletes - List all registered athletes (accessible to Staff: Coaches, Physios, Admins)
Logs & Analysis
POST /api/training - Log training load (Duration × RPE)
GET /api/training - Retrieve weekly training logs
POST /api/injuries - Add an Injury Record
GET /api/injuries - Retrieve historical injury logs
Video & Pipeline
POST /api/videos/upload - Upload training clip for pose estimation processing
GET /api/videos - Get video logs with skeletal frames and kinematics scores
DELETE /api/videos/{video_id} - Purge video from database and local storage
Reporting
GET /api/reports/csv/{athlete_id} - Export training data to Excel CSV format
GET /api/reports/pdf/{athlete_id} - Export comprehensive printable report (HTML format)


