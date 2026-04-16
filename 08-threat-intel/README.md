# Threat Intelligence

> IOC enrichment logs and threat context reports. Every IOC from every lab investigation is checked through three independent sources to move from a raw indicator to a confirmed threat verdict.
>
> ---
>
> ## Why Threat Intelligence Enrichment Matters
>
> A raw IP address from an alert tells you almost nothing on its own. Enrichment transforms it into actionable intelligence:
>
> | Before Enrichment | After Enrichment |
> |---|---|
> | Source IP: 192.168.56.30 | Internal lab IP — 0/90 VT engines — confirmed clean |
> | Unknown domain in phishing email | 47/90 VT engines flagged — AbuseIPDB 91% — OTX 3 pulses — MALICIOUS |
> | SHA256 hash from Sysmon event | Matches known Metasploit payload — MALICIOUS |
>
> Enrichment is what converts an alert into a triage decision.
>
> ---
>
> ## Enrichment Standard Applied to Every IOC
>
> Every IOC extracted from any lab investigation is checked through all three tools in this order:
>
> ### 1. VirusTotal (virustotal.com)
> - **What it checks:** IP addresses, domains, URLs, and file hashes against 90+ antivirus and threat intelligence engines
> - - **Key fields to record:** Detection count (X/90), Community score, Threat label, Last analysis date, Relations tab (linked infrastructure)
>   - - **Threshold used:** Above 5/90 engines = suspicious. Above 15/90 = likely malicious.
>    
>     - ### 2. AbuseIPDB (abuseipdb.com)
>     - - **What it checks:** IP address reputation based on community-reported abuse incidents
>       - - **Key fields to record:** Confidence of Abuse %, Abuse categories, Total reports, Last reported date
>         - - **Threshold used:** Above 80% confidence = block immediately and document
>          
>           - | Category Code | Meaning |
>           - |---|---|
>           - | 4 | DDoS Attack |
>           - | 14 | Port Scan |
>           - | 18 | Brute Force |
>           - | 21 | Web App Attack |
>           - | 22 | SSH Abuse |
>          
>           - ### 3. AlienVault OTX (otx.alienvault.com)
>           - - **What it checks:** Whether the IOC appears in any published threat intelligence pulses from the global security community
>             - - **Key fields to record:** Pulse count, Malware families, Related indicators, Geographic data
> - **Interpretation:** Higher pulse count = more widely recognized as a threat actor infrastructure
>
> - ---
>
> ## Files in This Folder
>
> | File | Description |
> |---|---|
> | `ioc-enrichment-log.md` | Master enrichment table for all IOCs from Labs 1-8 |
>
> ---
>
> ## IOC Enrichment Log Format
>
> Each entry in `ioc-enrichment-log.md` follows this structure:
>
> | IOC | Type | VT Score | AbuseIPDB % | OTX Pulses | Verdict | Source Lab |
> |---|---|---|---|---|---|---|
> | 192[.]168[.]56[.]30 | IP | 0/90 | 0% | 0 | Internal lab IP | Lab 1-5 |
> | [phishing domain] | Domain | 47/90 | 91% | 3 | MALICIOUS | Lab 7 |
>
> > **Note on the Kali lab IP (192.168.56.30):** This IP scores 0/90 on VirusTotal because it is a private RFC1918 address that has never been seen on the public internet. Documenting this shows I understand the difference between a clean result and an address that simply cannot be looked up publicly.
> >
> > ---
> >
> > ## MITRE ATT&CK Connection
> >
> > Threat intelligence maps directly to the MITRE ATT&CK framework:
> >
> > | Intel Type | What It Reveals | ATT&CK Use |
> > |---|---|---|
> > | IP reputation | Known C2 infrastructure | T1071 — Application Layer Protocol |
> > | File hash | Known malware family | T1059 — Command and Scripting Interpreter |
> > | Domain reputation | Phishing infrastructure | T1566 — Phishing |
> > | Abuse categories | Attack technique used | Multiple techniques |
