# Supabase Memory Refresh

A tiny GitHub Action that **pings the health endpoints of multiple projects once a day**.
Useful for keeping free-tier projects (Supabase, Render, Fly.io, etc.) awake so they
aren't paused for inactivity, and as a lightweight daily uptime check.

The endpoint URLs are stored in a **GitHub Actions secret** — never committed to the repo —
so they stay private even if the repository is public.

## How it works

- `.github/workflows/scheduled-sync-7c21.yml` runs on a daily cron (06:00 UTC) and can
  also be triggered manually from the **Actions** tab (**Run workflow**).
- It reads the `ENDPOINTS` secret, sends a `GET` to each URL, and prints only the HTTP
  status (the URL itself is never printed to the logs).
- The job **fails** if any endpoint returns a non-2xx/3xx status (or is unreachable).

## Configure your endpoints (secret)

1. Go to **Settings → Secrets and variables → Actions → New repository secret**.
2. Name it `ENDPOINTS`.
3. Paste one endpoint per line, with an optional label after the URL:

   ```
   https://your-project-1.example.com/api/health   project-1
   https://your-project-2.example.com/healthz       project-2
   ```

Lines starting with `#` and blank lines are ignored. To add/remove projects, just edit
the secret value — no code changes needed.

## Change the schedule

Edit the `cron` line in the workflow. Times are **UTC**. Use https://crontab.guru.
Example — twice a day at 06:00 and 18:00 UTC:

```yaml
schedule:
  - cron: "0 6,18 * * *"
```

> GitHub may delay scheduled runs during peak load, and disables schedules on repos with
> no activity for 60 days. A manual run or commit re-enables them.

## Run it now

Push to GitHub, add the `ENDPOINTS` secret, open the **Actions** tab, select
**Scheduled sync**, and click **Run workflow** to test before the first scheduled run.
