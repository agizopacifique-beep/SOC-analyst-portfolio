# Detection Engineering

> Writing detection logic — not just using it. This folder contains custom detection rules I authored, tested, and documented in my home lab.
>
> ---
>
> ## What Detection Engineering Is
>
> Detection engineering is the practice of building the rules that make SIEMs and EDR tools fire alerts. Most Tier 1 analysts only consume alerts. Detection engineers create them. Writing your own rules requires you to deeply understand the attacker technique — you cannot write a rule for behavior you do not understand.
>
> ---
>
> ## Rules in This Folder
>
> ### 1. Custom Wazuh Rule — Brute Force Detection (Rule ID 100001)
> **File:** `wazuh-rule-100001.xml`
>
> **What it detects:** Password brute force attacks — specifically when the same Windows account receives more than 10 failed login attempts (Event ID 4625) within a 60-second window.
>
> **How I built it:** I ran Hydra from Kali against my Windows 10 VM to generate real Event ID 4625 entries. I then wrote this rule in the Wazuh local rules file on Security Onion and confirmed it fired on the 10th attempt.
>
> **Why it matters:** This is a frequency-based threshold rule. It does not rely on a known attack signature — it detects the behavior pattern regardless of which tool the attacker uses.
>
> | Field | Value | Purpose |
> |---|---|---|
> | `rule id` | 100001 | Unique identifier for this custom rule |
> | `if_matched_sid` | 18152 | Base rule to count (one fire per Event 4625) |
> | `frequency` | 10 | Number of base rule triggers needed |
> | `timeframe` | 60 | Counting window in seconds |
> | `same_field` | targetUserName | All 10 failures must target the same account |
> | `level` | 12 | Severity (12 = high priority alert) |
> | `mitre id` | T1110 | MITRE ATT&CK: Brute Force |
>
> ---
>
> ### 2. Sigma Rule — PsExec Lateral Movement
> **File:** `sigma-rules/psexec-lateral-movement.yml`
>
> **What it detects:** Lateral movement via PsExec by matching Event ID 7045 with ServiceName = PSEXESVC. PsExec always installs this service on the target machine, making it a reliable indicator.
>
> **How I built it:** I ran psexec.py from Kali against Windows 10 and confirmed Event 7045 fires with ServiceName: PSEXESVC. I then wrote this Sigma rule and used sigma-cli to convert it to Splunk SPL automatically.
>
> **Why Sigma specifically:** Sigma is a vendor-neutral rule format. One rule converts to Splunk SPL, Elastic KQL, QRadar AQL, or any other SIEM. Writing in Sigma means the rule works in any enterprise environment, not just my lab.
>
> | Field | Value |
> |---|---|
> | MITRE Technique | T1021.002 — Remote Services: SMB/Windows Admin Shares |
> | Log Source | Windows Security Event Log |
> | Detection Field | EventID: 7045, ServiceName: PSEXESVC |
> | Severity | High |
> | False Positive Rate | Low — legitimate admins occasionally use PsExec |
>
> ---
>
> ## Tools Used
>
> | Tool | Purpose |
> |---|---|
> | Wazuh Rule Engine | Hosts and evaluates custom XML detection rules |
> | Security Onion | Displays rule alerts in the Alerts tab |
> | Sigma (YAML format) | Universal detection rule language |
> | sigma-cli | Converts Sigma rules to SIEM-specific query syntax |
> | Splunk SPL | Receives converted Sigma rule as a saved alert |
