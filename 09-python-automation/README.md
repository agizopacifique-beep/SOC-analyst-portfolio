# Python Automation

> Two Python scripts that automate the most time-consuming parts of SOC analyst work — IOC extraction from raw alert logs and real-time threat enrichment via the VirusTotal API. Automation skills separate candidates who understand the job from those who only know how to click through tools.
>
> ---
>
> ## Why Automation Matters in a SOC Role
>
> Manual IOC triage is slow. When an alert fires with 50 log entries, extracting IPs, checking each one in VirusTotal, and recording results manually takes 20-30 minutes. These scripts reduce that to under 60 seconds.
>
> Real SOC environments use SOAR (Security Orchestration, Automation and Response) platforms like Splunk SOAR, Palo Alto XSOAR, or custom Python scripts to automate exactly this kind of repetitive work. These scripts demonstrate that skillset at a fundamental level.
>
> ---
>
> ## Script 1: ioc_extractor.py
>
> **Purpose:** Parses Wazuh JSON alert logs, filters high-severity alerts, and automatically extracts all IOCs (IP addresses, domains, and MD5 hashes).
>
> **How to run:**
> ```bash
> python3 ioc_extractor.py /path/to/alerts.json
> ```
>
> **Example — run against real Wazuh alerts:**
> ```bash
> python3 ioc_extractor.py /var/ossec/logs/alerts/alerts.json
> ```
>
> **Sample output:**
> ```
> IOC Extractor | Run time: 2025-04-16 09:32:14
> Parsing file: /var/ossec/logs/alerts/alerts.json
> ------------------------------------------------------------
> [2025-04-16T09:31:02.441+0000]
>   Rule: CUSTOM RULE: Brute Force Detected - 10+ failed logins
>   Severity Level: 12
>   IOC -> ip: 203.0.113.47
>   IOC -> ip: 198.51.100.23
>
> ------------------------------------------------------------
> Total alerts parsed:        847
> High severity alerts (>=10): 12
> Total IOCs extracted:        8
> Unique external IPs found:   3
> IPs to check in VirusTotal: ['203.0.113.47', '198.51.100.23', '192.0.2.15']
> ```
>
> **What it does step by step:**
> 1. Opens the Wazuh alerts.json file and reads it line by line
> 2. 2. Parses each line as a JSON alert object
>    3. 3. Filters to only process alerts with severity level 10 or higher
>       4. 4. Uses regex patterns to extract: IPv4 addresses, domain-like strings, MD5 hashes
>          5. 5. Skips private/internal IP ranges (10.x, 192.168.x, 172.16-31.x, 127.x)
>             6. 6. Prints a clean report and lists all external IPs ready for VirusTotal lookup
>               
>                7. ---
>               
>                8. ## Script 2: vt_enricher.py
>               
>                9. **Purpose:** Takes any IP address or file hash as a command-line argument and queries the VirusTotal API to return a threat verdict, detection count, country of origin, and ownership information.
>
> **Requirements:** Free VirusTotal API key — sign up at virustotal.com and get your key from account settings.
>
> **How to run:**
> ```bash
> # Check an IP address
> python3 vt_enricher.py 203.0.113.47
>
> # Check a file hash (MD5 or SHA256)
> python3 vt_enricher.py d41d8cd98f00b204e9800998ecf8427e
> ```
>
> **Sample output for an IP:**
> ```
> ==================================================
> Checking IP: 203.0.113.47
>   Verdict:    MALICIOUS
>   Score:      47/90 engines flagged as malicious
>   Country:    RU
>   Owner:      AS12345 Example Hosting Provider
>   Last seen:  2025-04-14
>   VT Link:    https://www.virustotal.com/gui/ip-address/203.0.113.47
> ==================================================
> ```
>
> **Sample output for a file hash:**
> ```
> ==================================================
> Checking hash: d41d8cd98f00b204e9800998ecf8427e
>   Verdict:    MALICIOUS
>   Score:      61/72 engines flagged
>   Filename:   shell.exe
>   File size:  73802 bytes
>   VT Link:    https://www.virustotal.com/gui/file/d41d8cd98f00b204e9800998ecf8427e
> ==================================================
> ```
>
> **What it does step by step:**
> 1. Takes the input and detects whether it is an IP address or a hash (by length and character set)
> 2. 2. Makes an authenticated API request to the VirusTotal v3 API
>    3. 3. Parses the JSON response for detection statistics
>       4. 4. Calculates a verdict: CLEAN (0-5 engines), SUSPICIOUS (6-14), or MALICIOUS (15+)
>          5. 5. Prints a formatted one-screen report with the direct VirusTotal link
>            
>             6. ---
>            
>             7. ## Files in This Folder
>            
>             8. | File | Description |
> |---|---|
> | `ioc_extractor.py` | Parses Wazuh JSON alerts and extracts IOCs automatically |
> | `vt_enricher.py` | Queries VirusTotal API for threat verdict on any IP or hash |
> | `sample-output-ioc.txt` | Sample terminal output from ioc_extractor.py against real lab alerts |
> | `sample-output-vt.txt` | Sample terminal output from vt_enricher.py against lab IOCs |
>
> ---
>
> ## Combined Workflow (How I Use Both Scripts Together)
>
> ```bash
> # Step 1: Extract all IOCs from Wazuh alerts after a lab exercise
> python3 ioc_extractor.py /var/ossec/logs/alerts/alerts.json
>
> # Step 2: Take each external IP from the output and enrich it
> python3 vt_enricher.py 203.0.113.47
> python3 vt_enricher.py 198.51.100.23
>
> # Step 3: Copy verdicts into the GitHub report's IOC section
> # This entire process takes under 2 minutes vs 20+ minutes manually
> ```
>
> ---
>
> ## Technical Details
>
> | Detail | Value |
> |---|---|
> | Language | Python 3 |
> | External libraries | None — uses only Python standard library (json, re, sys, urllib) |
> | API used | VirusTotal v3 REST API (free tier) |
> | VirusTotal rate limit | 4 requests per minute on free tier |
> | Compatible with | Linux, macOS, Windows (any Python 3.6+) |
>
> > **No external libraries required.** Both scripts use only Python's built-in standard library. No pip install needed. This makes them portable across any SOC environment without dependency management.
