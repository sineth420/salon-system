# Salon backend (step 1)

Node.js + Express + PostgreSQL. Covers: free-times calculation, 15-minute holds with a
database-level no-overlap rule, PayHere checkout fields, and the verified PayHere
notification that confirms a booking.

## Run locally
1. Install Node 18+ and PostgreSQL 13+.
2. `createdb salon` then `psql salon -f sql/schema.sql -f sql/seed.sql`
3. `cp .env.example .env` and edit DATABASE_URL.
4. `npm install`
5. `npm test`  (pure logic tests, no database needed)
6. `npm start`

## Booking page
After `npm start`, open **http://localhost:3000/demo-salon** in your browser.
Pick a service, a day and a time, enter a name and phone, then press Pay.
Paying needs your PayHere sandbox merchant ID and secret in the `salons` table (see below).

## Try it (API)
- `GET  /api/salons/demo-salon`
- `GET  /api/salons/demo-salon/availability?date=2026-12-02&staffId=1&packageId=1`
- `POST /api/bookings` with JSON: slug, staffId, packageId, start (ISO time from availability),
  customerName, customerPhone. Returns checkoutUrl + fields to POST to PayHere.
- PayHere calls `POST /api/payhere/notify` (needs a public HTTPS URL; use a tunnel for sandbox tests).

## Before going live
- Put your real PayHere sandbox merchant ID/secret in `salons` and test the sandbox flow.
- Verify the hash formulas in `src/payhere.js` against PayHere's current docs.
- Encrypt `payhere_secret` at rest. Serve behind HTTPS (Caddy/Nginx).
- Payments with a non-null `review_note` need manual attention (refund / late payment).

## Not built yet
Admin login and dashboard, WhatsApp messages and reminders, cancel/reschedule, booking page UI.
