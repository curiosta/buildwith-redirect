# buildwith.curiosta.com redirect stub (plan only, not deployed)

Nomad moved from `buildwith.curiosta.com` to `nomad.curiosta.com` (release 2026-10-26a). GitHub Pages serves one custom domain per repo, so the old host needs its own tiny repo. This folder is that repo's full content.

What it does
- `index.html`, `calendar/index.html`: meta refresh to `https://nomad.curiosta.com/` plus a JS redirect that keeps path, query and `#hash`.
- `404.html`: GitHub Pages serves it for every other path on the old host, and its JS sends the visitor to the same path on `nomad.curiosta.com` (e.g. `/experiences/hoi-an-lantern-workshop/` goes to the same page). With JS off it falls back to the new home page.
- `calendar/experiences.ics` and `calendar/experiences_2026_2027.ics`: a copy of the new calendar file, served at the old URL so existing `webcal://buildwith.curiosta.com/calendar/experiences.ics` subscriptions keep working (calendar apps do not follow meta or JS redirects). Event UIDs are the same as before, so subscribers get no duplicates. The calendar name and "More:" links now say Nomad and the new host.
- `CNAME`: `buildwith.curiosta.com`. `robots.txt`: allow all (so search engines can follow the refresh). Tested locally: 12 of 12 redirect checks (JS on and off) and the ICS (69 events).

Limits: GitHub Pages cannot send a real 301, so search engines treat this as a soft redirect. The ICS copy is a snapshot: when the calendar changes, copy `calendar/experiences.ics` and `calendar/experiences_2026_2027.ics` from the new release into this repo and push, until you decide to retire the old URL.

## Steps (do them in one sitting, in this order)

### 1. AWS Admin (Route 53, zone `curiosta.com`)
1. Create `nomad.curiosta.com` as a CNAME to `curiosta.github.io` (TTL 300).
2. Leave the existing `buildwith.curiosta.com` CNAME to `curiosta.github.io` as it is. The old host keeps working through the stub repo.
3. Check: `dig +short nomad.curiosta.com CNAME` returns `curiosta.github.io.`

### 2. GitHub Admin: move the domain on the Nomad repo (`curiosta/buildwith`, name unchanged)
1. Only after Manoj approves publishing release 2026-10-26a in chat: commit the full release folder to `main` (its `CNAME` file now says `nomad.curiosta.com`).
2. Settings > Pages > Custom domain: set `nomad.curiosta.com`, wait for the DNS check, then tick Enforce HTTPS (the certificate can take up to an hour).
3. Giscus: the repo, category and ids are unchanged. If the repo has a `giscus.json` with an `origins` list, add `https://nomad.curiosta.com`.

### 3. GitHub Admin: create the stub repo for the old host
1. Create a new public repo, for example `curiosta/buildwith-redirect`, and push the contents of this folder to `main` (README.md may be dropped).
2. Settings > Pages: deploy from `main` / root. Custom domain: `buildwith.curiosta.com`. The domain is free to bind once step 2 has moved it off `curiosta/buildwith`. Tick Enforce HTTPS when it is offered.
3. If the org uses GitHub "verified domains" for `curiosta.com`, both hosts are already covered.

### 4. After both are live (checks)
- `https://nomad.curiosta.com/` loads, shows "Nomad" and "Don't just watch. Take part."; `https://nomad.curiosta.com/calendar/experiences.ics` returns the file (69 events).
- `https://buildwith.curiosta.com/` and `https://buildwith.curiosta.com/experiences/uttarayan-ahmedabad/` land on the same pages on `nomad.curiosta.com`.
- `curl -s https://buildwith.curiosta.com/calendar/experiences.ics | head -3` shows `BEGIN:VCALENDAR`.
- Expect a short gap on the old host between steps 2 and 3, and up to an hour for the HTTPS certificates.
