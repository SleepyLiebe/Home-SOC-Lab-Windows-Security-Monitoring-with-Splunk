# Detection logic

## 1. Windows repeated failed logons

**File:** `windows-repeated-failed-logons.spl`  
**Source:** Windows Security Event Log, `sourcetype=WinEventLog:Security`  
**Event:** Event ID `4625` (failed logon)  
**Window / schedule:** Five-minute search window; alert configured on a five-minute cron schedule (`*/5 * * * *`).  
**Trigger:** Search returns at least one grouped host result; email action enabled.

The current rule counts matching records by `host`. It is intentionally simple and does not distinguish source IPs, accounts, logon types, or network-vs-interactive logons. Those would be useful tuning additions.

## 2. Linux repeated SSH authentication failures

**File:** `metasploitable-ssh-failures.spl`  
**Source:** Metasploitable `sysklogd` remote syslog; `sourcetype=syslog`; target host in this lab is `192.168.159.129`.  
**Events matched:** `Failed password`, `authentication failure`, and `Failed publickey`.  
**Grouping:** Extracted source IP and username; threshold `failures >= 3`.  
**Window / schedule:** Five-minute lab window and five-minute alert schedule.  
**Validation:** Controlled failed SSH authentication attempts from Kali produced a matching result and the alert email was received.

**Tuning note:** `Failed password` and PAM `authentication failure` can be two log records for the same attempt. This is a count of records, not a verified count of distinct attempts. For higher-fidelity detection, normalize and deduplicate records or count only a single authoritative event pattern.

## 3. Sysmon process-creation review

**File:** `../queries/sysmon-process-creation.spl`  
**Source:** `Microsoft-Windows-Sysmon/Operational`  
**Event:** Event ID `1` (process creation).  
**Validation:** A controlled `notepad.exe` launch was visible in Splunk; Image and CommandLine were extracted into fields.

This is a hunting/review query, not an alert. It is useful as a starting point for future process-based detections after a baseline is established.
