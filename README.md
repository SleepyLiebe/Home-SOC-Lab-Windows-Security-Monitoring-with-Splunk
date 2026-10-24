# Home-SOC-Lab-Windows-Security-Monitoring-with-Splunk
Home SOC lab using Splunk Enterprise to collect Windows Security logs, detect repeated failed logins, and send automated email alerts.

# Home SOC Lab: Windows Security Monitoring with Splunk

## Project Overview

I built a small Security Operations Center (SOC) home lab to practice security event collection, log analysis, detection engineering, and alerting. The environment uses VMware Workstation Pro to run an Ubuntu-based Splunk Enterprise server and a Windows Server machine that forwards Windows event logs to Splunk.

The objective was to create and validate a basic detection for repeated failed Windows logins and configure email notifications when the detection threshold was met.

## Objectives

- Build a virtualized environment for security monitoring.
- Centralize Windows event logs in Splunk Enterprise.
- Search and analyze Windows Security events.
- Develop a detection for repeated failed logins.
- Configure a scheduled alert and email notification.
- Validate the detection using controlled activity on a dedicated lab account.

## Lab Architecture

**Windows Server → Splunk Universal Forwarder → Splunk Enterprise on Ubuntu → Detection Alert → Email Notification**

### Components

| Component | Purpose |
|---|---|
| VMware Workstation Pro | Hosts the virtual machines |
| Ubuntu Server | Runs Splunk Enterprise |
| Windows Server | Generates Windows Security events |
| Splunk Universal Forwarder | Sends Windows logs to Splunk |
| Splunk Enterprise | Receives, indexes, searches, and analyzes events |
| Gmail SMTP | Delivers alert notifications |

The Splunk receiving port is configured as TCP 9997. The Splunk Web interface uses port 8000.

## Implementation

### 1. Virtual Machine Setup

Created an Ubuntu Server virtual machine and a Windows Server virtual machine using VMware Workstation Pro. Configured virtual CPU, memory, storage, and networking settings appropriate for the available host resources.

### 2. Splunk Enterprise Installation

Installed Splunk Enterprise on Ubuntu Server, configured its initial administrator account, and enabled a receiving port for forwarded data.

### 3. Windows Log Forwarding

Installed Splunk Universal Forwarder on the Windows Server virtual machine and configured it to forward Windows event logs to the Ubuntu Splunk server. Selected the relevant Security, System, and Application event logs during setup.

### 4. Event Verification

Verified that Windows Security events arrived in Splunk. Inspected event details and confirmed that fields such as `EventCode` were available for analysis. Observed Event ID 4672 in the collected data.

### 5. Failed-Login Detection

Generated a controlled failed-login event using a dedicated test account. Confirmed that a Windows Security event with Event ID 4625 appeared in Splunk.

The following SPL query counts failed-login events by host and returns hosts with at least three events in the selected search window:

```spl
index=* sourcetype="WinEventLog:Security" EventCode=4625
| stats count AS failed_logins by host
| where failed_logins >= 3
```

For the scheduled alert, configured a five-minute search window and a five-minute schedule. The alert triggers when the search returns at least one result.

### 6. Email Alerting

Configured outgoing email using Gmail SMTP and a Google app password. Successfully tested email delivery and then validated the failed-login alert by generating test activity on the dedicated lab account.

## Validation Results

| Test | Observed result |
|---|---|
| Splunk Web access | Successful |
| Windows Security log collection | Events appeared in Splunk |
| Event ID 4672 inspection | Observed in event details |
| Controlled failed-login test | Event ID 4625 appeared |
| Failed-login alert | Triggered during testing |
| Email notification | Received successfully |

These results validate the basic end-to-end pipeline in this lab. They do not constitute a complete security assessment or production-readiness certification.

## Security Considerations

- Kept the exercise within virtual machines I control.
- Used a dedicated test account for failed-login testing rather than intentionally risking the primary Administrator account.
- Used a Google app password for SMTP authentication rather than the regular Gmail password.
- Disabled the temporary test account after testing.
- Kept the lab network isolated from unnecessary external access.
- Avoided including passwords, app passwords, authentication tokens, or other secrets in project documentation.

## Challenges and Lessons Learned

During setup, I encountered issues involving VM configuration, first boot, Splunk installation, and service verification. Troubleshooting involved checking service status, validating IP connectivity, confirming the receiving configuration, and testing searches before building the alert.

The project strengthened my understanding of log forwarding, Windows Security event IDs, SPL searches, scheduled detections, SMTP configuration, and end-to-end validation.

## Future Improvements

- Create a Splunk dashboard showing failed logins and event trends.
- Add a detection for repeated failures grouped by account and source information.
- Collect and analyze additional Windows events using Sysmon.
- Configure Active Directory in a separate lab phase.
- Improve alert documentation and measure detection and notification timing.

## Evidence

Screenshots and sanitized configuration evidence will be stored in the `screenshots/` directory.

No credentials, app passwords, or other secrets should be included in this repository.

