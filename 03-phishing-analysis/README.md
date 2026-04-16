# Phishing Analysis

> End-to-end phishing investigation reports using the same workflow a Tier 1 SOC analyst follows for every phishing ticket — from raw email headers through IOC enrichment to a final verdict with containment actions.
>
> ---
>
> ## Investigation Workflow
>
> Every phishing report in this folder follows this exact sequence:
>
> | Step | Action | Tool Used |
> |---|---|---|
> | 1 | Obtain phishing sample | PhishTank.org |
> | 2 | Analyze email headers — find true sender IP, check SPF/DKIM/DMARC | MXToolbox Email Headers |
> | 3 | Extract all IOCs — defang every IP, URL, domain, and email address | Manual extraction |
> | 4 | Check each URL safely without clicking | UrlScan.io |
> | 5 | Check sender IP against 90 AV engines | VirusTotal |
> | 6 | Check sender IP abuse confidence score | AbuseIPDB |
> | 7 | Check IOC against threat intel pulses | AlienVault OTX |
> | 8 | Write verdict with evidence and containment actions | Investigation report |
>
> ---
>
> ## What Defanging Means
>
> All IOCs in these reports are written in **defanged format** to prevent accidental clicking when reports are shared or forwarded:
>
> | Original | Defanged |
> |---|---|
> | `http://evil.com/steal` | `hxxp://evil[.]com/steal` |
> | `192.168.1.100` | `192[.]168[.]1[.]100` |
> | `attacker@evil.com` | `attacker@evil[.]com` |
>
> ---
>
> ## Authentication Checks Explained
>
> | Check | PASS Means | FAIL Means |
> |---|---|---|
> | **SPF** | The sending IP is authorized to send for this domain | Email may be spoofed or sent from unauthorized server |
> | **DKIM** | The email signature is cryptographically valid | Signature broken or missing — content may have been modified |
> | **DMARC** | The domain's authentication policy was followed | Policy violated — high risk of spoofing |
>
> When all three fail simultaneously, the email is almost certainly malicious or spoofed.
>
> ---
>
> ## Reports in This Folder
>
> | File | Sample Source | Verdict | Key Evidence |
> |---|---|---|---|
> | `phish-report-1.md` | PhishTank | Malicious | SPF FAIL + DKIM FAIL + VT 45/90 + AbuseIPDB 87% |
>
> ---
>
> ## Tools Used
>
> | Tool | URL | Purpose |
> |---|---|---|
> | MXToolbox | mxtoolbox.com/EmailHeaders | Email header analysis — SPF, DKIM, DMARC |
> | UrlScan.io | urlscan.io | Safe URL analysis — screenshots phishing page in sandbox |
> | VirusTotal | virustotal.com | IP/domain/hash check against 90 AV engines |
> | AbuseIPDB | abuseipdb.com | IP abuse confidence score and category |
> | AlienVault OTX | otx.alienvault.com | Threat intel pulse count and malware family links |
> | PhishTool | phishtool.com | Automated phishing email analysis |
> | PhishTank | phishtank.org | Source of real phishing samples for practice |
>
> ---
>
> ## MITRE ATT&CK Coverage
>
> | Tactic | Technique | ID |
> |---|---|---|
> | Initial Access | Phishing | T1566 |
> | Initial Access | Spearphishing Attachment | T1566.001 |
> | Initial Access | Spearphishing Link | T1566.002 |
