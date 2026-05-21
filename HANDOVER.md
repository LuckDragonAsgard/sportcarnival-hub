# sportcarnival-hub — HANDOVER (2026-05-21)

**Platform-level handover (read first):** https://raw.githubusercontent.com/LuckDragonAsgard/asgard-handovers/main/sportportal.md

## What this repo is
Cloudflare Worker that serves sportcarnival.com.au — carnival event pages (Athletics, Swimming, Cross Country), marshal apps, nav banner, privacy policy, and D1 REST API. Part of the School Sport Portal (SSP) platform built by Luck Dragon Pty Ltd (ABN 64 697 434 898).

## Live domains
- https://sportcarnival.com.au — apex
- https://www.sportcarnival.com.au — same worker (CF custom domain)

## Deployment
- Platform: Cloudflare Workers
- Worker name: `sportcarnival-hub` (DO NOT rename — custom domains are bound to this name)
- Trigger: push to `main` → GitHub Actions → `npx wrangler@4 deploy --keep-vars`
- Workflow file: `.github/workflows/deploy.yml`
- Migrated from Vercel: 2026-05-21

## GitHub Secrets (repo-scoped, SSP-isolated)
- `CLOUDFLARE_API_TOKEN` = SSP-dedicated token with Workers:Edit scope only
  - Token name in CF dashboard: `ssp-workers-deploy`
  - CRITICAL: this token must NEVER be shared with or replaced by any Falkor/Asgard project token
- `CLOUDFLARE_ACCOUNT_ID` = `a6f47c17811ee2f8b6caeb8f38768c20` (Luck Dragon Main)

## Cloudflare account
- Account name: Luck Dragon Main
- Account ID: `a6f47c17811ee2f8b6caeb8f38768c20`
- Worker: `sportcarnival-hub`
- Custom domains on worker: sportcarnival.com.au, www.sportcarnival.com.au
- Zone: sportcarnival.com.au (DNS managed in CF)

## Key files
- `worker.js` — main Worker entry point (all route handling)
- `wrangler.toml` — CF config (name, compat date, D1 binding)
- `.github/workflows/deploy.yml` — GitHub Actions deploy pipeline

## wrangler.toml (critical values)
```toml
name = "sportcarnival-hub"
main = "worker.js"
compatibility_date = "2025-04-01"
```

## Routes served
- `/williamstownps/Athletics26` — WPS Athletics 2026 marshal app
- `/williamstownps/Swim26` — WPS Swimming 2026 marshal app
- `/williamstownps/XC26` → 302 → `/district/primary/williamstown/XC26`
- `/district/primary/williamstown/XC26` — Williamstown District XC marshal app
- `/api/list` — list all carnivals (D1)
- `/api/results?carnival=CODE` — results for a carnival (D1)
- `/api/status` — D1 API health check
- `/privacy` — privacy policy (APP 8 compliant as of 2026-05-13)
- Legacy: wps-athletics-2026.pages.dev → meta-refresh → /williamstownps/Athletics26

## D1 database
- Binding name: `DB` (in wrangler.toml)
- Database: `carnival-results-db`
- Database ID: `4c39e40c-b6ca-40db-83bb-e8c69bad6537`
- Tables: carnivals, results, users, auth_tokens, scores
- Marshal write access: PIN auth (get `CARNIVAL_PUBLISH_PIN` from Vault)

## Firebase (legacy — district app only)
- Still used by district coordinator app (district-sport/index.html) for seasons/fixtures/ladders
- DB: willy-district-sport-default-rtdb.asia-southeast1.firebasedatabase.app
- Region: Singapore (asia-southeast1) — non-compliant with APP 8, migration to D1 pending post-carnival
- Anon writes: locked (401). Public reads on /fl, /wps_aths_2026 still open (timing fallback).
- Service account key: Vault → FIREBASE_ADMIN_KEY_WILLY_DISTRICT_SPORT

## Privacy policy (FIXED 2026-05-13)
- Firebase region correctly stated as Singapore (asia-southeast1), not Australian region
- Previous wording was a compliance risk under APP 8 (cross-border disclosure)

## Carnival day checklist
1. Get `CARNIVAL_PUBLISH_PIN` from Vault (ask Paddy verbally for vault PIN first)
2. Open sportcarnival.com.au/williamstownps/Athletics26 on marshal device
3. Enter PIN when prompted (once per session per device)
4. Results publish to carnivaltiming.com within ~5s
5. Post-carnival: verify at sportcarnival.com.au/api/results?carnival=WPSAT

## Related workers (do not confuse)
- `carnival-results` — REST API over D1, PIN auth, session auth, cron jobs, forgot-password. Runs at carnival-results.pgallivan.workers.dev
- `carnival-timing-html` — carnivaltiming.com live results viewer
- `ssp-portal` — schoolsportportal.com.au (separate repo, separate worker)
- `district-proxy` — district.luckdragon.io 301 redirect

## Outstanding / pending
- ⚠ District app Firebase migration — post-carnival, needs D1 schema for seasons/fixtures/ladders/teams
- ⚠ WPSAT test data (Liam Chen etc) in D1 — replace with real results on carnival day

## To resume
1. Read platform-level handover (link at top of this file)
2. Get vault PIN from Paddy verbally
3. Pull credentials from vault as needed
4. Do not rename the `sportcarnival-hub` worker or move custom domains
