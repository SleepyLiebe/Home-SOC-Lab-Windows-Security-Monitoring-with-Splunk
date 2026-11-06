# Lab architecture and data flow

## Virtual machines

| System | Address observed in this lab | Role |
|---|---|---|
| Ubuntu Server / Splunk Enterprise | NAT `192.168.42.129`; Host-only `192.168.159.130` | Central SIEM, TCP 9997 receiver, UDP 1514 syslog input, Splunk Web on 8000, scheduled alert actions |
| Windows Server 2022 Standard Evaluation | NAT-side lab address observed as `192.168.42.130` | Windows Security events and Sysmon Operational channel; Splunk Universal Forwarder |
| Kali Linux | Host-only `192.168.159.128` | Analyst/test workstation; Nmap service discovery and controlled SSH-failure generation |
| Metasploitable 2 | Host-only `192.168.159.129` | Intentionally vulnerable Linux target; sysklogd, SSH/PAM logs, HTTP/Tomcat services |

These are private RFC1918 addresses from the observed VMware configuration; they may differ when the VMs are recreated or when VMware networking is changed.

## Data flows

1. **Windows endpoint → Splunk:** The Splunk Universal Forwarder forwards Windows Security events and `Microsoft-Windows-Sysmon/Operational` over TCP port `9997` to Splunk Enterprise.
2. **Sysmon → Splunk:** Sysmon v15.22 records process lifecycle activity. Event ID 1 was confirmed for a controlled Notepad launch; Event ID 5 was also observed.
3. **Kali → Metasploitable:** Kali and Metasploitable share the VMware Host-only network `192.168.159.0/24`. Nmap service-version detection was used to inventory the target, and controlled failed SSH logins were used to generate authentication logs.
4. **Metasploitable → Splunk:** The older `sysklogd` configuration forwards syslog to Ubuntu's Host-only address on UDP port `514`. Ubuntu has a runtime `iptables` PREROUTING redirect on interface `ens37` from UDP/514 to UDP/1514. Splunk's local UDP input listens on port `1514` with `sourcetype=syslog`. This path was validated with a marker message and real DHCP/CRON/SSH/PAM messages.
5. **Splunk → email:** Splunk uses the Ubuntu VM's NAT connection to connect to Gmail SMTP at `smtp.gmail.com:587` with TLS. The notification workflow was validated after renewing the Google App Password and ensuring `gmail.com` was permitted in the email-domain allow-list. No secret is included in this repository.

## Security boundary

- Kali and Metasploitable remain on Host-only networking. Do not set Metasploitable to Bridged networking or expose it to the internet.
- Ubuntu has both NAT and Host-only adapters: the private interface supports lab ingestion, while NAT supports package access and outbound SMTP.
- UDP syslog is unencrypted/unauthenticated and should remain inside the isolated lab.
- The `iptables` redirect shown here was added at runtime and may not persist after reboot unless explicitly saved. Verify the rule after restarting Ubuntu.
