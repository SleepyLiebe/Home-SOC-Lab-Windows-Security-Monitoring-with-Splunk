# Validation and observed results

This document records tests that were performed in the lab, using screenshots and event searches from the session. It distinguishes confirmed behavior from planned extensions.

| Test | Evidence / result | Status |
|---|---|---|
| Windows Security ingestion | Splunk returned Windows Security Event ID 4625 records from the Windows Server host. | Verified |
| Windows repeated-failed-logon detection | SPL grouped Event ID 4625 records by host with a threshold of at least 3 events in a five-minute window. The scheduled alert ran and an email notification was received. | Verified |
| Sysmon installation and logging | Sysmon64 service was running. Event Viewer contained Event ID 1 (process creation) and Event ID 5 (process termination). A Notepad process creation event was visible in Splunk. | Verified |
| Sysmon process query | A search extracted executable path and command line fields and returned process events. | Verified |
| Kali-to-Metasploitable network | `ping` from Kali to `192.168.159.129` succeeded over Host-only networking. | Verified |
| Service discovery | Nmap service-version detection listed multiple open services on Metasploitable 2. HTTP on port 80 and Tomcat on port 8180 were viewed manually. | Verified |
| Splunk UDP input | Splunk accepted a test syslog marker from Kali on UDP/1514. | Verified |
| Metasploitable syslog forwarding | A marker message and real syslog records from Metasploitable appeared in Splunk as `sourcetype=syslog`, `source=udp:1514`. | Verified |
| SSH/PAM failure logging | Controlled failed SSH logins from Kali produced `Failed password` and PAM `authentication failure` records on Metasploitable; Splunk parsed source IP and username groups. | Verified |
| Linux repeated SSH-failure alert | The scheduled Metasploitable SSH alert was received by email after the Gmail SMTP settings were corrected. | Verified |
| SMTP email delivery | A direct test email was received after renewing the Google App Password and correcting the active allowed-domain configuration. | Verified |
| Metasploit exploitation | The session focused on testing the SSH alert; no completed exploit execution is documented in this stage. | Not claimed |
| Malware / credential-dumping simulation | No malware payload or credential-dumping tool was executed in this stage. | Not claimed |

## Validated Windows detection

```spl
index=* earliest=-5m sourcetype="WinEventLog:Security" EventCode=4625
| stats count AS failed_logins by host
| where failed_logins >= 3
```

## Validated Linux detection

The rule is stored in [`../detections/metasploitable-ssh-failures.spl`](../detections/metasploitable-ssh-failures.spl). It uses the target host value observed in this lab and must be adjusted if Splunk identifies the host differently in another environment.

## Limitations and tuning opportunities

- The Windows detection currently counts Event ID 4625 records by host rather than grouping by source IP/user. It is a learning rule that can be refined.
- The Linux rule counts matching log records, which may include both sshd and PAM messages for a single login attempt. For production-style detection, normalize event categories and deduplicate by timestamp/session/source/user before interpreting the count as distinct attempts.
- Queries use `index=*` because this build indexed the data in the current default environment. A next step is to create dedicated indexes and update retention and search scope.
- The Ubuntu redirect from UDP/514 to UDP/1514 should be made persistent only after documenting and reviewing the host's firewall policy.
- The syslog input uses UDP, so messages are not authenticated or encrypted and delivery is not guaranteed.
