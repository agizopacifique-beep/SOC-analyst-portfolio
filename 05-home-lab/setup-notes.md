# Home Lab Setup Notes
## Agizo Pacifique — SOC Analyst Portfolio

---

## Host Machine

| Component | Specification |
|---|---|
| Model | HP EliteDesk 800 G4 Desktop Mini |
| Processor | Intel Core i5-8500T — 6 Cores — 2.1 GHz |
| RAM | 32 GB DDR4 |
| Storage | 1 TB NVMe M.2 SSD |
| Operating System | Windows 11 Pro (64-bit) |
| Virtualization | Oracle VirtualBox 7.x |
| Network Type | Host-Only — 192.168.56.0/24 (fully isolated from internet) |

---

## Virtual Machine Assignments

| VM | Role | RAM | CPU | Storage | IP Address |
|---|---|---|---|---|---|
| Security Onion 2.4 | SIEM + IDS + Wazuh Manager | 8 GB | 4 cores | 120 GB | 192.168.56.10 |
| Kali Linux | Attacker + Splunk Host | 4 GB | 2 cores | 50 GB | 192.168.56.30 |
| Windows 10 Pro | Primary Target Endpoint | 4 GB | 2 cores | 80 GB | 192.168.56.20 |
| Ubuntu Server 22.04 | Secondary Linux Target | 2 GB | 1 core | 40 GB | 192.168.56.40 |
| **TOTAL ASSIGNED** | | **18 GB** | **9 vCPUs** | **290 GB** | |
| **HOST KEEPS** | | **14 GB** | Shared | **710 GB** | |

> **Note on CPU oversubscription:** VirtualBox dynamically schedules virtual CPUs across the 6 physical cores. VMs never all run at full load simultaneously, making 9 vCPUs across 6 physical cores completely stable and standard practice.

---

## Tools Installed Per VM

### Security Onion (192.168.56.10)
- **Suricata** — Network IDS using signature-based detection rules
- **Zeek** — Automatic structured network log generation (conn.log, dns.log, http.log)
- **Elasticsearch + Kibana** — Log storage, indexing, and visual dashboards
- **Wazuh Manager** — Receives and processes endpoint agent logs
- **Security Onion Hunt** — Unified search across all log sources

### Kali Linux (192.168.56.30)
- **nmap** — Network and port scanner (Labs 1)
- **Hydra** — Password brute force tool (Lab 2)
- **Metasploit Framework + msfvenom** — Exploit framework and payload generator (Lab 3)
- **Impacket / psexec.py** — Lateral movement simulation (Lab 4)
- **Wireshark + tcpdump** — Packet capture and protocol analysis (Lab 5)
- **python3-pyftpdlib** — FTP server for exfiltration simulation (Lab 5)
- **Splunk Enterprise** — Second SIEM for SPL query-based detection (port 8000)
- **Python 3** — Automation scripts for IOC extraction and VirusTotal enrichment

### Windows 10 Pro (192.168.56.20)
- **Sysmon with SwiftOnSecurity config** — Enhanced endpoint logging (process creation, network connections, file events, DNS queries)
- **Wazuh Agent** — Forwards Windows Event Logs and Sysmon data to Security Onion in real time

### Ubuntu Server 22.04 (192.168.56.40)
- **Wazuh Agent** — Forwards auth.log and syslog to Security Onion

---

## Lab Network Architecture

```
+---------------------------+        +---------------------------+
|   Kali Linux              |        |   Windows 10 Pro          |
|   192.168.56.30           |        |   192.168.56.20           |
|   Attack tools + Splunk   +------->+   Sysmon + Wazuh Agent    |
+---------------------------+        +---------------------------+
          |                                       |
                    |  Attack traffic                       |  Endpoint logs
                              v                                       v
                              +-------------------------------------------------------------+
                              |                   Security Onion 2.4                        |
                              |                   192.168.56.10                             |
                              |   Suricata (network IDS) | Zeek (network logs)             |
                              |   Wazuh Manager (endpoint logs) | Elasticsearch + Kibana   |
                              |   Hunt tab: unified search across ALL log sources          |
                              +-------------------------------------------------------------+
                                        ^
                                                  |  Endpoint logs
                                                  +---------------------------+
                                                  |   Ubuntu Server 22.04     |
                                                  |   192.168.56.40           |
                                                  |   Wazuh Agent             |
                                                  +---------------------------+

                                                  All VMs communicate on: 192.168.56.0/24 (Host-Only — isolated from internet)
                                                  ```

                                                  ---

                                                  ## Key Windows Event IDs Monitored

                                                  | Event ID | Description | Lab Used |
                                                  |---|---|---|
                                                  | 4624 | Successful Logon | Lab 2, Lab 4 |
                                                  | 4625 | Failed Logon (brute force detection) | Lab 2 |
                                                  | 4648 | Logon with Explicit Credentials (PsExec) | Lab 4 |
                                                  | 4688 | Process Creation | Lab 3, Lab 4 |
                                                  | 7045 | New Service Installed (PSEXESVC) | Lab 4 |

                                                  ## Key Sysmon Event IDs Monitored

                                                  | Event ID | Description | Lab Used |
                                                  |---|---|---|
                                                  | 1 | Process Created (full path, parent, hash, command line) | Lab 3, Lab 4 |
                                                  | 3 | Network Connection (process-to-IP mapping) | Lab 3 |
                                                  | 11 | File Created | Lab 3 |
                                                  | 13 | Registry Value Modified | Lab 4 |
                                                  | 22 | DNS Query | Lab 7 |

                                                  ---

                                                  ## Setup Timeline

                                                  | Milestone | Status |
                                                  |---|---|
                                                  | VirtualBox installed + Host-Only network created | Complete |
                                                  | Security Onion installed and dashboard accessible | Complete |
                                                  | Kali Linux installed and attack tools ready | Complete |
                                                  | Windows 10 Pro installed with static IP 192.168.56.20 | Complete |
                                                  | Ubuntu Server 22.04 installed with static IP 192.168.56.40 | Complete |
                                                  | Sysmon installed on Windows with SwiftOnSecurity config | Complete |
                                                  | Wazuh Agent connected on Windows (green dot in Security Onion) | Complete |
                                                  | Splunk installed on Kali and accessible at port 8000 | Complete |
                                                  | First live detection confirmed (nmap alert in Security Onion) | Complete |
