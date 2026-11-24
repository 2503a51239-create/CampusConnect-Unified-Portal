# CampusConnect 2.0 — Campus Event Management Platform

A mobile-first campus event platform designed with separate student and administration portals, QR event discovery, digital tickets, attendance check-in, and coordinator assignment. The API is versioned (`/api/v1`) to make a future mobile client easier to add.

## Requirements
- Node.js 20 or newer
- npm

## Run locally
1. Extract the ZIP and open this folder in VS Code.
2. Open the integrated terminal in this folder.
3. Run `npm install`.
4. Copy `.env.example` to `.env` (optional for local demo; recommended to change credentials and JWT secret).
5. Run `npm start`.
6. Visit `http://localhost:3000`.

## Demo administrator
- URL: `http://localhost:3000/admin`
- Email: `admin@campusconnect.local`
- Password: `ChangeMe123!`

Set `ADMIN_EMAIL`, `ADMIN_PASSWORD`, and a long random `JWT_SECRET` in `.env` before using outside a local demo. The first administrator is seeded automatically when the database has no admin account. The seed values are read only at first initialization.

## Portals
- Student portal: `/`
- Event QR landing page: `/event/:slug`
- Admin / coordinator portal: `/admin`

## Core workflows
### Student
1. Browse published events without signing in.
2. Create an account using name, student ID, college email and password.
3. Register for an event in one action.
4. Open **My registrations** to view the unique ticket code and ticket QR.
5. Receive in-app notifications and submit event feedback.

### Admin
1. Sign in at `/admin`.
2. Create and publish events.
   - **Edit events** (title, category, venue, date/time, capacity, description) from Events → Edit. Capacity cannot go below the number of confirmed registrations; registered students are notified when the schedule or venue changes. Cancelled events cannot be edited.
3. Generate a QR code for each event. Scanning opens that event's page directly.
4. Create coordinator accounts and assign them to specific events.
5. Review registrations and export CSV.
6. Check in participants by scanning ticket QR or entering the ticket code.
7. Review student feedback.

### Coordinator
A coordinator can sign in through `/admin`, see assigned events and registrations, generate the assigned event QR and check in attendees for assigned events only. Coordinators cannot create events, create staff accounts or assign other coordinators.

## Architecture
- Front end: semantic HTML, responsive CSS, vanilla JavaScript.
- Server: Node.js + Express.
- API: REST endpoints under `/api/v1`.
- Authentication: bcrypt password hashes, signed JWT in an HttpOnly cookie, role checks on server routes.
- QR: `qrcode` package. Event QR links to the event registration page; attendee ticket QR contains a unique ticket code.
- Persistence: local JSON store in `data/db.json` for a zero-configuration classroom demo.

## Important production upgrade notes
The included JSON store is intended for a single-process classroom prototype, not production or multi-instance deployment. Before a real campus rollout, migrate the storage layer to PostgreSQL or MongoDB, add database transactions/unique constraints, email/OTP verification, password reset, audit logs, backups, HTTPS, CSRF protection, privacy/retention policies, and a managed notification provider. Use an institutional identity provider if available. Browser QR scanning requires camera permission and a secure context (localhost or HTTPS). On a phone, open the QR event URL using the computer's LAN IP while both devices are on the same Wi-Fi; set `APP_BASE_URL` to that reachable address for correct QR generation.

## Main API groups
- `/api/v1/auth/*` — student registration, sign-in, sign-out, current user
- `/api/v1/events/*` — public event discovery, registration, feedback, event QR
- `/api/v1/me/*` — student registrations and notifications
- `/api/v1/admin/*` — overview, event management, staff, assignments, registrations, attendance and feedback

## Notes
- Sample events are seeded on first run when no events exist.
- No real payment processing is included; the project is designed for free campus event registration.
- Admin demo credentials must be changed before deployment.


## One shared website link
Open or share `http://localhost:3000`. The welcome page lets each visitor choose Student Portal or Administration. Student features are at `/student`, and administration login is at `/admin`. Event QR links continue to open the relevant student event page directly.


## Attendance check-in rule
Check-in is accepted only on the event's scheduled date (from the start date to the end date, inclusive). Early and late check-ins are rejected with a clear message. Dates are evaluated in the campus timezone set by `CAMPUS_TIMEZONE` in `.env` (default `Asia/Kolkata`).
