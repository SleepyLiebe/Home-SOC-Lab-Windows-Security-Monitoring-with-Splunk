# Screenshot evidence

All screenshots in this folder were taken from the lab session or reused from the previously prepared, redacted Windows evidence set. No app passwords or SMTP credential screens are included.

## Windows evidence

1. `01-windows-security-event-4625.png` — Expanded Windows Security failed-logon event.
2. `02-windows-failed-login-search.png` — Search evidence for Event ID 4625.
3. `03-windows-alert-configuration.png` — Saved scheduled alert configuration.
4. `04-windows-alert-email-received.png` — Redacted evidence of a Windows alert email.
5. `05-sysmon-process-create-event.png` — Sysmon Event ID 1 showing a controlled Notepad process creation.
6. `06-sysmon-events-in-splunk.png` — Sysmon events searchable in Splunk.

## Linux evidence

1. `01-nmap-service-enumeration.png` — Nmap service-version discovery against the isolated Metasploitable target.
2. `02-metasploitable-http-homepage.png` — Metasploitable HTTP landing page.
3. `03-metasploitable-tomcat-homepage.png` — Apache Tomcat page on port 8180.
4. `04-syslog-forwarding-verified.png` — Test syslog marker received by Splunk.
5. `05-linux-system-logs-in-splunk.png` — Metasploitable DHCP/CRON messages in Splunk.
6. `06-ssh-failed-authentication-events.png` — SSH/PAM failed authentication events.
7. `07-linux-ssh-detection-results.png` — Detection result grouping the source IP, username, and event count.

Before committing, review every image for details you prefer not to publish. Private lab IPs are shown in some screenshots for technical context; you may redact them if desired. Do not upload passwords, App Passwords, tokens, or screenshots of credential forms.
