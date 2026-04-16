# Network Analysis

> Packet capture, protocol analysis, and network-based threat detection. This folder contains PCAP analysis reports, Wireshark investigation notes, and Snort/Suricata detection rules written and tested in my home lab.
>
> ---
>
> ## Core Concept: Why Packet Analysis Matters
>
> Network logs tell you that a connection happened. Packet captures tell you exactly what was transferred. The difference is critical for:
>
> - **Data exfiltration cases** — logs show a connection to an FTP server. The PCAP shows the actual file contents that were stolen.
> - - **Compliance violations** — logs show FTP was used. The PCAP proves cardholder data crossed the wire in cleartext, triggering PCI DSS notification requirements.
>   - - **Malware C2 traffic** — logs show an outbound TCP connection. The PCAP shows the command-and-control protocol being used.
>    
>     - ---
>
> ## Tools Used
>
> | Tool | Purpose | How I Used It |
> |---|---|---|
> | **tcpdump** | Command-line live packet capture | Captured FTP exfiltration traffic to a .pcap file during Lab 5 |
> | **Wireshark** | Graphical PCAP analysis and protocol dissection | Opened captures, applied display filters, followed TCP streams |
> | **Zeek** | Automatic structured log generation from network traffic | Read conn.log for connection duration and bytes transferred |
> | **Suricata** | Real-time network IDS with signature-based alerting | Fired automatic alerts during nmap scans and FTP transfers |
> | **Snort** | Rule-based IDS | Wrote custom Snort rules and tested them against .pcap files |
>
> ---
>
> ## Files in This Folder
>
> | File | Lab | Description |
> |---|---|---|
> | `wireshark-pcap-analysis.md` | Lab 5 | FTP data exfiltration analysis — Wireshark TCP stream showing stolen PII in plaintext |
> | `snort-rules.md` | Lab 5 | Custom Snort rules written and tested against the Lab 5 PCAP capture |
> | `pcap-files/` | Lab 5 | The actual .pcap capture file from the FTP exfiltration |
> | `screenshots/` | Lab 5 | Evidence screenshots from Wireshark, tcpdump, and Security Onion |
>
> ---
>
> ## Key Analysis Technique: Wireshark Display Filters
>
> Display filters narrow a full PCAP down to only the traffic relevant to the investigation:
>
> | Filter | What It Shows | When to Use |
> |---|---|---|
> | `ftp` | FTP command channel — login credentials, file commands | Find the FTP session and stolen filename |
> | `ftp-data` | FTP data channel — actual file contents being transferred | Read the stolen data |
> | `tcp.port == 4444` | All traffic on port 4444 | Investigate Meterpreter C2 connections |
> | `http` | All HTTP traffic | Investigate web-based C2 or data exfiltration |
> | `dns` | All DNS queries | Detect DNS tunneling or unusual domain lookups |
> | `ip.addr == 192.168.56.30` | All traffic to or from Kali | Scope investigation to attacker traffic |
>
> ### How to Follow a TCP Stream
> Right-click any packet → **Follow** → **TCP Stream**
>
> This reassembles the full conversation between client and server and displays it in human-readable text. For FTP data transfers, this shows the complete file contents — exactly as they appeared on the wire with no decryption needed.
>
> ---
>
> ## Lab 5 Key Finding
>
> The Wireshark TCP Stream analysis of the FTP exfiltration proved:
>
> 1. FTP transmits file contents with **zero encryption**
> 2. 2. An attacker or insider with network access could read the stolen data in real time
>    3. 3. The stolen file contained SSN and credit card numbers — both protected under **PCI DSS** and **GLBA**
>       4. 4. Using FTP for any data containing cardholder information is a direct compliance violation
>         
>          5. ---
>         
>          6. ## MITRE ATT&CK Coverage
>         
>          7. | Tactic | Technique | ID |
> |---|---|---|
> | Exfiltration | Exfiltration Over Alternative Protocol | T1048 |
> | Exfiltration | Exfiltration Over Unencrypted Non-C2 Protocol | T1048.003 |
> | Discovery | Network Service Discovery | T1046 |
> | Command and Control | Non-Application Layer Protocol | T1095 |
