# Growth Intelligence ingestion layer

## What it does
- Pulls Instagram + TikTok Organic content analytics from Windsor.
- Normalizes both sources into `content_performance`.
- Upserts on `(platform, platform_content_id)`, so re-syncs do not duplicate posts.
- Extracts `NWS-...` Content IDs from captions when present.
- Preserves raw provider JSON for debugging/backfills.
- Exposes `/api/dashboard` for the UI.
- Provides `/api/conversions` as the Whop-ready conversion ingestion endpoint.

## Run locally
1. `npm install`
2. Copy `.env.example` to `.env` and add the Windsor API key on the server only.
3. Load environment variables and run `npm run api`.
4. POST `/api/sync?days=30` to ingest the latest 30 days.
5. GET `/api/dashboard` for normalized dashboard data.

## Production schedule
Run `POST /api/sync?days=7` every 6 hours. The 7-day overlap catches delayed platform metrics while the upsert prevents duplicates.

## Whop-ready attribution
Send normalized conversion events to `/api/conversions` with `content_id`, `event_type`, `event_id`, `revenue`, `currency`, and `occurred_at`. When Whop is wired in, its webhook handler should validate Whop's signature first, then transform the event into this schema.

## Security
Never place Windsor or Whop secrets in React/Vite environment variables shipped to the browser. Production secrets belong in server/hosting secret storage.
