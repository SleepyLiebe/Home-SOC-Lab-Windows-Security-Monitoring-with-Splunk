# SOC Home Lab Screenshots

This folder contains selected evidence from a home SOC lab using VMware Workstation Pro, Ubuntu Server, Splunk Enterprise, Windows Server, and Splunk Universal Forwarder.

## Screenshot index

1. **`01-splunk-server-startup.png`** — Splunk Enterprise startup checks completed on Ubuntu and the Splunk Web interface became available.
2. **`02-windows-security-event-4672.png`** — Example Windows Security event details in Splunk, including EventCode 4672.
3. **`03-failed-login-event-4625.png`** — Expanded Windows Security failed-logon event (Event ID 4625).
4. **`04-failed-login-search-4625.png`** — Search evidence for Windows Security Event ID 4625.
5. **`05-repeated-failed-logins-alert-config.png`** — Scheduled alert configuration showing a five-minute schedule, five-minute search window, and a trigger condition that fires when the search returns results.
6. **`06-email-alert-received.png`** — Email notification confirming the repeated failed Windows logins alert condition was triggered.

## Privacy

The Splunk web address containing the private lab IP address has been redacted where visible. The recipient/sender Gmail address in the notification screenshot has also been redacted. Review screenshots again before posting publicly to ensure no credentials, tokens, personal data, or other identifying details are exposed.

## Notes

The screenshots document actual steps and outputs captured during the lab. The failed-login search screenshot is evidence of searching for Event ID 4625; it is not a screenshot of the full threshold SPL query. The detection query can be documented separately in the main README or a `queries/` folder.
