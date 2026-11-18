# South Sudan NBMIS and Hospital Request Portal

A professional React + Vite frontend for the South Sudan National Blood Bank Management Information System.

## Setup

1. Copy `.env.example` to `.env`.
2. Set `VITE_API_BASE_URL` to the backend API (the attached backend defaults to `http://localhost:4000/api`).
3. Keep `VITE_ENABLE_MOCK_API=false` to use the live backend.

## Hospital portal setup

The separate hospital portal is served by this frontend at `/hospital/<facility-slug>`. It uses the shared MySQL database and API in the `BLOOD BANK` backend project; it is not a standalone or mock-data portal.

1. In `BLOOD BANK`, apply `server/db/schema.sql`, then `server/db/hospital_portal.sql`.
2. Set the backend `CLIENT_ORIGIN` to the exact frontend origin(s) and `PORTAL_BASE_URL` to the origin that hospitals will open.
3. Start the backend and this frontend. Sign in as a national administrator and use **Hospital Management** to register, verify and approve a facility.
4. Deliver the displayed one-time activation URL to the facility manager using an approved secure channel. No email/SMS delivery provider is configured.
5. The manager activates an account with a password of at least 12 characters. Hospital accounts can only submit and track their own requests and read their own notifications.

The national administrator can monitor facility and request activity from the national dashboard and **Hospital Management**, review requests, allocate partial quantities from shared component stock, update statuses, send feedback, adjust inventory and inspect audit events.

## Backend workflows

The API client in `src/lib/api.js` connects the operational screens to these backend routes:

- Donors: `GET` and `POST /donors`, `PATCH /donors/:id`
- Hospital requests: `GET` and `POST /requests`, `PATCH /requests/:id` (edit pending request), `PATCH /requests/:id/status`
- Inventory: `GET /inventory`, `POST /inventory/issue`
- Dashboard: `GET /dashboard`

Donor registration adds the entered units to inventory. Approving a hospital request allocates its units immediately; the Blood Issuance screen issues units directly from inventory. Declining a request does not change stock.

The login screen uses the backend administrator session. Hospital and administrator sessions are stored server-side and use HttpOnly cookies. Hospital endpoints enforce facility-scoped access; legacy national API routes require an administrator session. The administrator credential format is still legacy SHA-256, so this pilot must not be used for production or real patient records without the security work listed in the backend documentation.

Hospital portal authentication, request submission, status tracking, notifications, facility management, inventory allocation and audit operations require the shared database migration. If the database is unavailable, the portal API returns an error rather than accepting requests into temporary in-memory storage.

## Local development

Run the backend in `BLOOD BANK` and the frontend in this project in separate terminals. Configure the backend's `CLIENT_ORIGIN` to include the exact frontend origin (for example `http://localhost:5173,http://localhost:5174`) for credentialed CORS, and set `PORTAL_BASE_URL` to the same origin that will host the hospital portal.

```bash
npm run dev
```

## Production build

```bash
npm run build
```
