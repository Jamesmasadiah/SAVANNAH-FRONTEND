 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/README.md b/README.md
index 6c494d52e06696cd702abd9af8e949e67decf122..8f0938b5675daa0f4d604b8cc7aab694ed292710 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,83 @@
-# SAVANNAH-FRONTEND
-# SAVANNAH-FRONTEND
+# Savannah PMS Frontend
+
+A React + Vite frontend for the **Savannah Property Management System**, focused on:
+- authentication (login/registration),
+- dashboard-style rent/transaction views,
+- M-Pesa rent payment initiation and status tracking.
+
+## Tech Stack
+- React 18
+- Vite 5
+- Native Fetch API for backend communication
+
+## Project Structure
+- `src/main.jsx` — app bootstrap.
+- `src/App.jsx` — main UI, auth flow, dashboard widgets, transaction table, API helpers.
+- `src/MpesaPayment.jsx` — M-Pesa STK push flow and payment status polling.
+- `src/index.css` — global animation styles.
+
+## Core Functional Bits
+
+### 1) Authentication
+Implemented in `src/App.jsx`:
+- **Login** via `POST /api/auth/login`
+- **Register** via `POST /api/auth/register`
+- Stores session data (`token`, `user`) in `sessionStorage` on success.
+
+### 2) API Layer
+In `src/App.jsx`, a lightweight `api` object wraps backend calls:
+- `login(email, password)`
+- `register(name, email, password, role)`
+- `get(path, token)`
+- `post(path, body, token)`
+
+Base URL resolution:
+- `VITE_API_URL` if set
+- fallback: `https://savannah-backend-kcxm.onrender.com`
+
+### 3) Dashboard & Data Presentation
+`src/App.jsx` includes reusable UI units:
+- `StatCard` for KPI blocks
+- `BarChart` for expected vs collected rent
+- `Donut` for percentage indicators
+- `TransactionTable` with status color coding (`Completed`, `Pending`, `Failed`)
+
+### 4) M-Pesa Payment Process
+In `src/MpesaPayment.jsx`:
+1. User enters phone number (`+254` prefix handled in UI).
+2. Frontend sends `POST /api/mpesa/stkpush` with tenant/property/payment payload.
+3. If accepted, frontend stores `checkout_request_id` and starts polling.
+4. Polling calls `GET /api/mpesa/status/{checkoutId}` every 3 seconds (up to 30 tries).
+5. UI transitions through states: `sending` → `pending` → `completed` / `failed` / `timeout`.
+
+> Note: `MpesaPayment.jsx` currently reads auth token from `localStorage`, while auth in `App.jsx` writes to `sessionStorage`. Aligning these stores is recommended for consistency.
+
+## Environment Variables
+Create a `.env` file at repo root:
+
+```env
+VITE_API_URL=https://your-backend-url
+```
+
+If omitted, default URLs in components are used.
+
+## Run Locally
+```bash
+npm install
+npm run dev
+```
+
+## Build for Production
+```bash
+npm run build
+npm run preview
+```
+
+## Backend API Endpoints Used
+- `POST /api/auth/login`
+- `POST /api/auth/register`
+- `POST /api/mpesa/stkpush`
+- `GET /api/mpesa/status/{checkoutId}`
+
+---
+If you are extending this frontend, start with `src/App.jsx` (main flow) and `src/MpesaPayment.jsx` (payment lifecycle).
 
EOF
)
