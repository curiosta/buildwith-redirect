# buildwith.curiosta.com redirect only

Nomad lives at nomad.curiosta.com. This repo serves only a redirect for the old host buildwith.curiosta.com.

- index.html: refresh and JS redirect to https://nomad.curiosta.com/
- 404.html: every other path (including /calendar/ and old .ics URLs) lands here and is sent to the same path on nomad.curiosta.com
- CNAME, robots.txt, favicon.ico

No calendar files or other content are hosted here. Decision by Manoj, 26 Oct 2026.
