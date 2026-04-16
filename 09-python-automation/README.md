# Python Automation

After running Lab 8 and manually checking eight IOCs one by one through VirusTotal, AbuseIPDB, and OTX — copy the IP, open the tab, wait, read the result, write it down, repeat — I decided that was not a good use of time. I wrote these two scripts to handle the repetitive parts.

Neither script requires anything outside Python's standard library. No pip install, no virtual environment setup. They run on any machine with Python 3.

---

## ioc_extractor.py

**What it does:** Reads a Wazuh alerts JSON log, finds all high-severity alerts (level 10 and above), and pulls every IP address out of them. It skips private RFC1918 ranges (192.168.x, 10.x, 172.16-31.x) since those are always internal lab IPs — no point checking those in VirusTotal.

**Why I built it:** Wazuh alerts.json can have thousands of entries. Finding the ones that matter and pulling the external IPs by hand is slow and error-prone. This does it in a few seconds.

**How to run it:**

```bash
python3 ioc_extractor.py /var/ossec/logs/alerts/alerts.json
```

**What the output looks like:**

```
IOC Extractor | 2025-04-16 09:32:14
Parsing: /var/ossec/logs/alerts/alerts.json
--------------------------------------------
[2025-04-16T09:31:02Z]
  Rule: CUSTOM RULE: Brute Force - 10+ failed logins in 60s
  Level: 12
  IOC -> ip: 203.0.113.47

--------------------------------------------
Alerts parsed:          847
High severity (>=10):    12
IOCs extracted:           8
External IPs to check:   3

Ready for VirusTotal: ['203.0.113.47', '198.51.100.23', '192.0.2.15']
```

---

## vt_enricher.py

**What it does:** Takes an IP address or file hash as a command-line argument and hits the VirusTotal v3 API. Returns the detection count, a clean/suspicious/malicious verdict, the country and owner if it's an IP, and the direct VT link so I can click through for more detail if needed.

**Requirements:** A free VirusTotal API key. Sign up at virustotal.com, go to your account settings, copy the API key, and paste it into the script where it says `API_KEY = ""`.

**How to run it:**

```bash
# Check an IP
python3 vt_enricher.py 203.0.113.47

# Check a file hash from Sysmon Event ID 1
python3 vt_enricher.py d41d8cd98f00b204e9800998ecf8427e
```

**What the output looks like:**

```
==================================================
IP: 203.0.113.47
  Verdict:   MALICIOUS
  Score:     47/90 engines
  Country:   RU
  Owner:     AS12345 Some Hosting Provider
  Last seen: 2025-04-14
  Link:      https://www.virustotal.com/gui/ip-address/203.0.113.47
==================================================
```

---

## How I Actually Use These Together

After finishing a lab investigation and pulling all my IOCs into the report, I do this:

```bash
# Step 1: Pull external IPs from Wazuh logs
python3 ioc_extractor.py /var/ossec/logs/alerts/alerts.json

# Step 2: Check each external IP
python3 vt_enricher.py 203.0.113.47
python3 vt_enricher.py 198.51.100.23

# Step 3: Copy the verdicts and scores into the IOC table in my report
```

The whole process takes about 90 seconds. Doing it manually through browser tabs was closer to 20 minutes.

---

## Files

| File | Description |
|---|---|
| `ioc_extractor.py` | Parses Wazuh JSON alerts, filters high severity, extracts external IPs |
| `vt_enricher.py` | Queries VirusTotal API for any IP address or file hash |
| `sample-output-ioc.txt` | Example terminal output from running ioc_extractor against real lab logs |
| `sample-output-vt.txt` | Example terminal output from running vt_enricher against lab IOCs |
