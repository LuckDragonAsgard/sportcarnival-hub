# sportcarnival-hub — HANDOVER

**Platform-level handover:** https://raw.githubusercontent.com/LuckDragonAsgard/asgard-handovers/main/sportportal.md

## What this repo is
Cloudflare Worker that serves sportcarnival.com.au — carnival event pages, marshal apps, nav banner, and D1 API endpoints. Part of the School Sport Portal (SSP) platform.

## Live site
- https://sportcarnival.com.au
- https://www.sportcarnival.com.au → same worker

## Deployment
- Platform: Cloudflare Workers (worker name: `sportcarnival-hub`)
- Trigger: push to `main` branch → GitHub Actions → `npx wrangler@4 deploy`
- Workflow: `.github/workflows/deploy.yml`
- Migrated from Vercel: 2026-05-21

## GitHub Secrets (repo-scoped, SSP-isolated)
- `CLOUDFLARE_API_TOKEN` — SSP-dedicated token, Workers:Edit only. MUST NOT be shared with Falkor/Asgard projects.
- `CLOUDFLARE_ACCOUNT_ID` — Luck Dragon Main account: `a6f47c17811ee2f8b6caeb8f38768c20`

## Cloudflare
- Account: Luck Dragon Main (`a6f47c17811ee2f8b6caeb8f38768c20`)
- Worker: `sportcarnival-hub`
- Custom domains: sportcarnival.com.au, www.sportcarnival.com.au

## Key routes
- `/williamstownps/Athletics26` — WPS Athletics marshal app
- `/williamstownps/Swim26` — WPS Swimming marshal app
- `/williamstownps/XC26` → 302 → `/district/primary/williamstown/XC26`
- `/district/primary/williamstown/XC26` — WD XC marshal app
- `/api/list` — list carnivals
- `/api/results?carnival=CODE` — results for a carnival
- `/api/status` — D1 API health

## Backend
- D1 database: `carnival-results-db` (`4c39e40c-b6ca-40db-83bb-e8c69bad6537`)
- Firebase Realtime DB (legacy, district coordinator app only): willy-district-sport.asia-southeast1
- PIN auth for marshal write access

## Carnival day
1. Get `CARNIVAL_PUBLISH_PIN` from Vault
2. Open sportcarnival.com.au/williamstownps/Athletics26 on marshal device
3. Enter PIN when prompted (once per session)
4. Results appear on carnivaltiming.com within ~5s

## To resume
1. Read platform handover linked above
2. Get PIN from Paddy verbally → pull any credential from vault
3. `wrangler.toml` has worker name and compat date — do not rename the worker
