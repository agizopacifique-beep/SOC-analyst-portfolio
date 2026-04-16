# Threat Intelligence

An IP address by itself tells you almost nothing. 192.168.56.30 is suspicious — until you realize it's the Kali VM I own and everything it did was deliberate. The value of threat intelligence is context: who else has seen this indicator, what category of abuse was reported, does this match known malicious infrastructure.

This folder is the enrichment log for every IOC I extracted across all eight labs.

---

## How I Approached Enrichment

I didn't just check every IOC in VirusTotal and call it done. I ran each one through three sources and compared what they showed:

**VirusTotal** — good for file hashes and known malicious domains. Detection count matters but so does *who* flagged it. A hash that shows 2/90 from two obscure engines is different from 2/90 from CrowdStrike and Kaspersky.

**AbuseIPDB** — better for understanding *behavior*. A 91% confidence score means hundreds of people reported that IP doing something bad. The category codes tell you what: port scanning, brute force, web attacks. More useful for IP reputation than VT in most cases.

**AlienVault OTX** — useful for seeing if an IOC is part of a larger campaign. If a phishing IP shows up in 12 different threat intel pulses across the last 90 days, that's a pattern, not a one-off.

---

## The Kali IP Problem

One thing worth documenting: `192[.]168[.]56[.]30` — Kali's IP — scores 0/90 on VirusTotal and 0% on AbuseIPDB. That's not because it's clean. It's because it's a private RFC1918 address that has never appeared on the public internet. No one has reported it because no one outside my lab has ever seen it.

I included it in the enrichment log anyway. Knowing *why* an indicator comes back clean is part of the analysis. A 0/90 result on a private IP means something completely different from a 0/90 result on a public IP.

---

## Files in This Folder

| File | Description |
|---|---|
| `ioc-enrichment-log.md` | Full enrichment table for every IOC from Labs 1-8 |
| `screenshots/` | VirusTotal, AbuseIPDB, and OTX result screenshots for key indicators |

---

## Enrichment Table Format

Each entry in the log looks like this:

| IOC | Type | VT Score | AbuseIPDB % | OTX Pulses | Verdict |
|---|---|---|---|---|---|
| 192[.]168[.]56[.]30 | IP | 0/90 | 0% | 0 | Internal lab — private range, not publicly routable |
| [phishing domain] | Domain | 47/90 | 91% | 3 | MALICIOUS |

The defanging (`[.]` instead of `.`) is deliberate — it prevents the IOC from being accidentally clicked if the report is pasted into an email or a ticket.

---

## Tools Used

| Tool | URL | What I Used It For |
|---|---|---|
| VirusTotal | virustotal.com | Hashes, IPs, URLs — detection count and vendor breakdown |
| AbuseIPDB | abuseipdb.com | IP abuse confidence score and reported categories |
| AlienVault OTX | otx.alienvault.com | Threat intel pulse count and associated malware families |
