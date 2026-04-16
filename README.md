# Hi, I'm Agizo Pacifique

I'm a cybersecurity professional based in Tampa, FL, currently finishing my BS in Cybersecurity while working toward my first SOC analyst role. I hold an active CompTIA Security+ and spend my evenings building and breaking things in my home lab.

This repo is where I document that work. Every lab in here is something I actually ran — I generated the attack, captured the evidence, traced it through logs, and wrote up what I found. No CTF writeups, no walkthroughs. Just real detection work on a real (virtual) network.

---

## Why I Built This Lab

I wanted to understand security from both sides. Reading about how a brute force attack works is one thing. Watching 847 Event ID 4625 entries flood into Security Onion in real time — while Hydra is still running in the other window — is something else entirely. That's the kind of understanding I'm building here.

The lab runs on a single HP EliteDesk 800 G4 Mini I picked up and loaded with 32 GB of RAM. Four VMs, all on an isolated Host-Only network so nothing escapes to my actual home network. Kali attacks. Windows and Ubuntu get hit. Security Onion catches it. I investigate it.

---

## Lab Setup

| Machine | Role | IP | RAM |
|---|---|---|---|
| Security Onion 2.4 | SIEM + IDS (Suricata, Zeek, Wazuh Manager) | 192.168.56.10 | 8 GB |
| Kali Linux | Attacker + Splunk | 192.168.56.30 | 4 GB |
| Windows 10 Pro | Primary target (Sysmon + Wazuh Agent) | 192.168.56.20 | 4 GB |
| Ubuntu Server 22.04 | Secondary target (Wazuh Agent) | 192.168.56.40 | 2 GB |

**Host:** HP EliteDesk 800 G4 — i5-8500T 6-Core — 32 GB RAM — 1 TB NVMe — Windows 11 Pro
**Network:** VirtualBox Host-Only 192.168.56.0/24 — fully isolated, nothing touches the internet

See the network diagram: [05-home-lab/network-diagram.md](05-home-lab/network-diagram.md)

---

## What's In Here

| Folder | What It Contains |
|---|---|
| `01-siem-investigations/` | Port scan and brute force detection — Suricata alerts, SPL queries, custom Wazuh rule |
| `02-network-analysis/` | Wireshark PCAP analysis — FTP exfiltration with file contents visible in plaintext |
| `03-phishing-analysis/` | Full phishing workflow — headers, IOC enrichment, SPF/DKIM/DMARC, verdict |
| `04-incident-reports/` | Reverse shell, lateral movement, data exfil — full PICERL reports with timelines |
| `05-home-lab/` | Lab setup, VM specs, and the Mermaid network diagram |
| `06-cloud-detection/` | AWS CloudTrail — failed logins, unauthorized IAM user creation, access key staging |
| `07-detection-engineering/` | Custom Wazuh rule 100001 (XML) and Sigma rule for PsExec (YAML) |
| `08-threat-intel/` | IOC enrichment log — every indicator from every lab run through VT, AbuseIPDB, OTX |
| `09-python-automation/` | Two Python scripts: IOC extractor and VirusTotal enricher |
| `10-certifications/` | Security+, Splunk Fundamentals 1, TryHackMe SOC Level 1 |

---

## Labs Completed

| # | Lab | Key Finding | Status |
|---|---|---|---|
| 1 | Port Scan Detection | Suricata signature + behavioral SPL query both caught the nmap scan | In Progress |
| 2 | Brute Force Detection | Wrote Wazuh Rule 100001 — fires on the 10th failed login within 60 seconds | In Progress |
| 3 | Reverse Shell / C2 | Traced shell.exe spawning cmd.exe in Sysmon — that parent-child pair is the tell | In Progress |
| 4 | Lateral Movement | Correlated Event IDs 4648 + 4624 Type 3 + 7045 to confirm PsExec | In Progress |
| 5 | Data Exfiltration | Followed the FTP TCP stream in Wireshark — SSN and card number in plaintext | In Progress |
| 6 | AWS Cloud Detection | Caught failed console logins, unauthorized IAM user creation, and access key staging | In Progress |
| 7 | Phishing Analysis | SPF fail + DKIM fail + 45/90 VT engines + AbuseIPDB 87% — verdict: malicious | In Progress |
| 8 | Threat Intel Enrichment | Enriched all IOCs from Labs 1-7 through VirusTotal, AbuseIPDB, and OTX | In Progress |

---

## Skills

| Area | Tools | Where to Look |
|---|---|---|
| SIEM triage | Security Onion, Splunk SPL | Labs 1-2 |
| Network analysis | Wireshark, tcpdump, Zeek, Suricata | Lab 5 |
| Endpoint detection | Sysmon, Wazuh, Windows Event Logs | Labs 3-4 |
| Incident response | PICERL methodology with full timelines | Labs 3-5 |
| Phishing | MXToolbox, UrlScan.io, PhishTool, VirusTotal | Lab 7 |
| Threat intelligence | VirusTotal, AbuseIPDB, AlienVault OTX | Lab 8 |
| Detection engineering | Custom Wazuh XML rules, Sigma YAML | Labs 2, 4 |
| Cloud security | AWS CloudTrail, CloudWatch Logs Insights | Lab 6 |
| Python | IOC extraction and VT API enrichment scripts | 09-python-automation/ |

---

## Certifications

- CompTIA Security+ — active
- - Splunk Core Fundamentals 1 — in progress (splunk.com/training — it's free)
  - - TryHackMe SOC Level 1 Path — in progress
   
    - ---

    ## Contact

    - Email: agizopacifique@gmail.com
    - - Phone: (727) 439-7012
      - - Location: Tampa, FL
