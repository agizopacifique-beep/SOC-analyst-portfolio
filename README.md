# Agizo Pacifique — SOC Analyst Portfolio

**CompTIA Security+  |  BS Cybersecurity (In Progress)  |  Tampa, FL**

---

## About This Portfolio

This repository documents hands-on cybersecurity investigations I conducted in my personal home lab environment. Each report follows a professional SOC investigation format including trigger, scope, timeline, root cause, IOCs, containment actions, MITRE ATT&CK tags, and defender recommendations.

---

## Lab Environment

| Component | Details | IP Address |
|---|---|---|
| SIEM + IDS | Security Onion 2.4 (Suricata, Zeek, Wazuh Manager) | 192.168.56.10 |
| Attacker Machine | Kali Linux (nmap, Hydra, Metasploit, Impacket) | 192.168.56.30 |
| Primary Target | Windows 10 Pro (Sysmon + Wazuh Agent installed) | 192.168.56.20 |
| Second Target | Ubuntu Server 22.04 (Wazuh Agent installed) | 192.168.56.40 |
| Second SIEM | Splunk Enterprise (SPL detection queries) | 192.168.56.30:8000 |

**Hardware:** HP EliteDesk 800 G4 — Intel Core i5-8500T 6-Core — 32 GB RAM — 1 TB NVMe SSD
**Virtualization:** Oracle VirtualBox — Host-Only Network 192.168.56.0/24

---

## What I Can Detect and Investigate

| Skill Area | Tools Used | Lab Evidence |
|---|---|---|
| SIEM Alert Triage | Security Onion, Splunk SPL | Lab 1, Lab 2 |
| Network Traffic Analysis | Wireshark, tcpdump, Zeek, Snort | Lab 5 |
| Endpoint Monitoring | Sysmon Event IDs, Wazuh, Windows Event Logs | Lab 3, Lab 4 |
| Incident Response | PICERL methodology, chain of custody, timelines | Lab 3, 4, 5 |
| Phishing Analysis | MXToolbox, PhishTool, UrlScan.io, VirusTotal | Lab 7 |
| Threat Intelligence | VirusTotal, AbuseIPDB, AlienVault OTX | Lab 8 |
| Detection Engineering | Custom Wazuh rules (XML), Sigma rules (YAML) | Lab 2, Lab 4 |
| Cloud Detection | AWS CloudTrail, CloudWatch Logs Insights | Lab 6 |
| Python Automation | IOC extraction script, VirusTotal API enrichment | Lab 9, Lab 10 |

---

## Completed Labs

| Lab | Title | Status |
|---|---|---|
| Lab 1 | Port Scan Detection — nmap + Suricata + Splunk SPL | In Progress |
| Lab 2 | Brute Force Detection + Custom Wazuh Rule 100001 | In Progress |
| Lab 3 | Reverse Shell / C2 Detection via Sysmon Process Tree | In Progress |
| Lab 4 | Lateral Movement Detection + Sigma Rule (PsExec) | In Progress |
| Lab 5 | Data Exfiltration + PCAP Analysis (Wireshark TCP Stream) | In Progress |
| Lab 6 | AWS CloudTrail Cloud Detection | In Progress |
| Lab 7 | Phishing Analysis Full Workflow | In Progress |
| Lab 8 | Threat Intelligence IOC Enrichment | In Progress |

---

## Repository Structure

```
soc-analyst-portfolio/
├── 01-siem-investigations/     <- Lab 1 + Lab 2 reports and screenshots
├── 02-network-analysis/        <- Wireshark PCAP reports and Snort rules
├── 03-phishing-analysis/       <- Phishing investigation reports and IOC tables
├── 04-incident-reports/        <- Labs 3, 4, 5 full PICERL incident reports
├── 05-home-lab/                <- Lab architecture, setup guide, VM specs
├── 06-cloud-detection/         <- AWS CloudTrail detection reports
├── 07-detection-engineering/   <- Custom Wazuh rules (XML) + Sigma rules (YAML)
├── 08-threat-intel/            <- IOC enrichment logs (VT + AbuseIPDB + OTX)
├── 09-python-automation/       <- ioc_extractor.py + vt_enricher.py scripts
└── 10-certifications/          <- Splunk Fundamentals 1 cert + TryHackMe badges
```

---

## Certifications

- CompTIA Security+ (Active)
- - Splunk Core Fundamentals 1 (in progress)
  - - TryHackMe SOC Level 1 Path (in progress)
   
    - ---

    ## Contact

    - **Email:** agizopacifique@gmail.com
    - - **Phone:** (727) 439-7012
      - - **Location:** Tampa, FL
