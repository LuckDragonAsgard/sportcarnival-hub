# sportcarnival.com.au

Source for the SportCarnival tool — part of the 4-domain SportPortal platform.

## Structure

- `index.html` — public landing page (scrubbed of specific school names)
- `williamstownps/` — login-gated carnival pages (real data hidden until auth is built)
- `CNAME` — points to sportcarnival.com.au
- `vercel.json` — route rewrites

Real carnival pages for paying schools go behind login in the next phase. For now, direct URLs show a login prompt.
