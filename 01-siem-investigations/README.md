# SIEM Investigations

> Alert triage and investigation reports using Security Onion and Splunk. Every report documents a real attack I generated in my home lab and investigated using the same workflow a Tier 1 SOC analyst follows in production.
>
> ---
>
> ## What This Folder Contains
>
> | File | Lab | Description |
> |---|---|---|
> | `lab1-port-scan-detection.md` | Lab 1 | Port scan detection using Suricata IDS + custom Splunk SPL behavioral alert |
> | `lab2-brute-force-detection.md` | Lab 2 | Brute force detection using Windows Event ID 4625 + custom Wazuh rule |
> | `screenshots/` | All labs | Evidence screenshots named descriptively for each investigation step |
>
> ---
>
> ## Investigation Methodology
>
> Every report in this folder follows the same 9-section professional SOC format:
>
> 1. **Trigger** — the alert or event that initiated the investigation
> 2. 2. **Scope** — all systems, users, IPs, and ports involved
>    3. 3. **Timeline** — attacker actions in chronological order with timestamps
>       4. 4. **Root Cause** — exactly how the attack was executed and why it succeeded
>          5. 5. **IOCs** — all indicators of compromise in defanged format
>             6. 6. **Lab Metrics** — specific numbers (event counts, duration, detection time)
>                7. 7. **Containment Actions** — what I would do to stop this in a real environment
>                   8. 8. **Improvements as a Defender** — specific controls that would prevent or detect faster
>                      9. 9. **MITRE ATT&CK Tags** — every finding tagged with technique ID
>                        
>                         10. ---
>                        
>                         11. ## Tools Used in This Folder
>                        
>                         12. | Tool | Role |
> |---|---|
> | Security Onion 2.4 | Primary SIEM — real-time alert triage and Hunt tab investigation |
> | Suricata | Network IDS — signature-based detection of nmap and attack patterns |
> | Zeek | Network log generation — conn.log, dns.log, http.log |
> | Wazuh | Endpoint detection — Windows Event Log forwarding and custom rules |
> | Splunk | Secondary SIEM — SPL query-based behavioral detection |
> | Kali Linux | Attack platform — nmap, Hydra |
> | Windows 10 Pro | Target endpoint — Sysmon + Wazuh Agent installed |
>
> ---
>
> ## MITRE ATT&CK Coverage
>
> | Lab | Tactic | Technique | ID |
> |---|---|---|---|
> | Lab 1 | Reconnaissance | Active Scanning: Scanning IP Blocks | T1595.001 |
> | Lab 1 | Discovery | Network Service Discovery | T1046 |
> | Lab 2 | Credential Access | Brute Force | T1110 |
> | Lab 2 | Credential Access | Password Guessing | T1110.001 |
