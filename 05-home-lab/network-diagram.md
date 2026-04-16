# Home Lab Network Diagram

> Visual network topology — HP EliteDesk 800 G4, Oracle VirtualBox, Host-Only network 192.168.56.0/24, fully isolated from the internet.
>
> ---
>
> ## Full Network Topology
>
> ```mermaid
> graph TB
>     subgraph HOST["HP EliteDesk 800 G4 — Windows 11 Pro Host"]
>         subgraph VBOX["Oracle VirtualBox 7.x"]
>             subgraph NET["Host-Only Network: 192.168.56.0/24"]
>                 KALI["Kali Linux\n192.168.56.30\nAttacker + Splunk"]
>                 SO["Security Onion 2.4\n192.168.56.10\nSIEM + IDS"]
>                 WIN["Windows 10 Pro\n192.168.56.20\nPrimary Target"]
>                 UBU["Ubuntu Server\n192.168.56.40\nSecondary Target"]
>             end
>         end
>     end
>
>     KALI -->|"Attack Traffic"| WIN
>     KALI -->|"Attack Traffic"| UBU
>     WIN -->|"Sysmon + Event Logs"| SO
>     UBU -->|"auth.log + syslog"| SO
>     KALI -.->|"Network Packets"| SO
>
>     style KALI fill:#4a1010,stroke:#f87171,color:#fca5a5
>     style SO   fill:#0f2e1a,stroke:#4ade80,color:#86efac
>     style WIN  fill:#101030,stroke:#60a5fa,color:#93c5fd
>     style UBU  fill:#2e2000,stroke:#fbbf24,color:#fde68a
> ```
>
> ---
>
> ## VM Specifications
>
> | VM | IP Address | Role | RAM | CPU | Disk | Key Tools |
> |---|---|---|---|---|---|---|
> | Security Onion 2.4 | 192.168.56.10 | SIEM + IDS + Wazuh Manager | 8 GB | 4 cores | 120 GB | Suricata, Zeek, Wazuh Manager, Elasticsearch, Kibana |
> | Kali Linux | 192.168.56.30 | Attacker + Splunk Host | 4 GB | 2 cores | 50 GB | nmap, Hydra, Metasploit, psexec.py, Wireshark, Splunk |
> | Windows 10 Pro | 192.168.56.20 | Primary Attack Target | 4 GB | 2 cores | 80 GB | Sysmon, Wazuh Agent |
> | Ubuntu Server 22.04 | 192.168.56.40 | Secondary Attack Target | 2 GB | 1 core | 40 GB | Wazuh Agent |
>
> ---
>
> ## Data Flow Explained
>
> ### Attack Traffic (solid arrows — Kali to Targets)
>
> | Lab | Tool | Target | Purpose |
> |---|---|---|---|
> | Lab 1 | nmap | Windows 10 | Port and service enumeration |
> | Lab 2 | Hydra | Windows 10 | SMB brute force (Administrator account) |
> | Lab 3 | Metasploit + msfvenom | Windows 10 | Reverse shell payload delivery on port 4444 |
> | Lab 4 | psexec.py (Impacket) | Windows 10 | Remote code execution via SMB |
> | Lab 5 | pyftpdlib FTP server | Windows 10 | Receives stolen sensitive.txt over FTP |
>
> ### Endpoint Log Flow (solid arrows — Targets to Security Onion)
>
> Wazuh Agent runs on both Windows 10 and Ubuntu. It reads local logs and forwards them in real time to Wazuh Manager inside Security Onion:
>
> - **Windows 10** sends: Sysmon events (EID 1, 3, 11, 13, 22) and Windows Security Events (EID 4624, 4625, 4648, 4688, 7045)
> - - **Ubuntu Server** sends: /var/log/auth.log and /var/log/syslog
>  
>   - ### Network Detection (dotted arrow — Kali traffic to Security Onion)
>  
>   - Security Onion passively monitors every packet on the 192.168.56.0/24 subnet:
>  
>   - - **Suricata** fires alerts when packets match known attack signatures (nmap, Meterpreter, FTP)
>     - - **Zeek** generates structured network logs automatically: conn.log, dns.log, http.log, ftp.log
>      
>       - ---
>
> ## IP Address Quick Reference
>
> | Device | IP Address | Access Method |
> |---|---|---|
> | Host PC (VirtualBox gateway) | 192.168.56.1 | Not a VM |
> | Security Onion | 192.168.56.10 | Browser: https://192.168.56.10 |
> | Windows 10 Pro | 192.168.56.20 | RDP / SMB |
> | Kali Linux | 192.168.56.30 | Splunk at http://192.168.56.30:8000 |
> | Ubuntu Server | 192.168.56.40 | SSH port 22 |
>
> ---
>
> ## Why Host-Only Network?
>
> | Mode | Internet Access | Home Router Sees Traffic | Attack Traffic Escapes |
> |---|---|---|---|
> | **Host-Only (this lab)** | No | No | No |
> | NAT | Yes | No | No |
> | Bridged | Yes | Yes | Possible |
>
> Host-Only keeps all attack traffic completely inside VirtualBox. Nothing from nmap, Hydra, Metasploit, or FTP exfiltration ever touches your home network or ISP.
