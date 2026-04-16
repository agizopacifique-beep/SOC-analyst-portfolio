# SIEM Investigations

These are the first two labs — the ones where I learned what a SIEM actually does vs what I thought it did. Security Onion does more than I expected. Splunk requires more from you than I expected. Running both at the same time for the same attack taught me things neither tool could teach alone.

---

## Lab 1 — Port Scan Detection

The attack: three nmap scans from Kali against Windows 10, run two minutes apart — a basic scan, then `-sV` for service versions, then `-A` for everything. The idea was to generate enough traffic to see how the two SIEMs respond differently.

**Security Onion side:** Suricata caught it automatically. Within about 60 seconds of the first nmap command the alert appeared in the Alerts tab — `ET SCAN Nmap Scripting Engine`. I expanded it and noted every field: source IP, destination, the rule name, severity, timestamp. Then I pivoted to the Hunt tab and searched for Kali's IP to see the full scope of traffic it generated across all log sources at once.

**Splunk side:** I wrote this SPL query from scratch:

```splunk
index=* src_ip="192.168.56.30"
| stats dc(dest_port) as unique_ports by src_ip
| where unique_ports > 50
```

`dc(dest_port)` counts distinct destination ports. If one IP touches more than 50 unique ports in a short window, that's scanning behavior — regardless of what tool did it. Saved it as a Splunk alert and confirmed it fired on its own the next time I ran nmap.

The thing that stuck with me: Suricata needs a matching signature to fire. My SPL query fires on the *behavior*. If someone wrote their own port scanner and Suricata had no rule for it, Suricata would miss it. The SPL query wouldn't.

**Report:** `lab1-port-scan-detection.md`

---

## Lab 2 — Brute Force Detection

The attack: Hydra running from Kali against the Windows 10 SMB service, trying every password in rockyou.txt against the Administrator account. I let it run for about 3 minutes before stopping it.

847 Event ID 4625 entries hit Security Onion in that time. I searched `event.code:4625` in the Hunt tab and watched them scroll. Expanded one to confirm it showed `TargetUserName: administrator` and `IpAddress: 192.168.56.30`.

Then I went into the Security Onion VM terminal and wrote Wazuh Rule 100001 — the custom brute force detection rule documented in `07-detection-engineering/`. Restarted Wazuh, ran Hydra again, and watched the rule fire exactly when it should: on the 10th failed attempt within 60 seconds against the same account.

I also built a parallel Splunk alert for the same behavior using SPL. Having both fire independently on the same attack is the kind of defense-in-depth that matters in real environments.

**Reports:**
- `lab2-brute-force-detection.md`
- - `07-detection-engineering/wazuh-rule-100001.xml` (the actual rule)
 
  - ---

  ## Files in This Folder

  | File | Description |
  |---|---|
  | `lab1-port-scan-detection.md` | Full investigation report — nmap, Suricata alert, Hunt tab pivot, SPL query |
  | `lab2-brute-force-detection.md` | Full investigation report — Hydra, Event 4625 flood, custom Wazuh rule, Splunk alert |
  | `screenshots/` | Evidence screenshots from both labs |

  ---

  ## Tools Used

  | Tool | What I Used It For |
  |---|---|
  | Security Onion — Alerts tab | Saw Suricata and Wazuh alerts fire in real time |
  | Security Onion — Hunt tab | Pivoted from alert IP to see all associated traffic |
  | Splunk SPL | Wrote behavioral detection queries — `dc(dest_port)`, `stats count by src_ip` |
  | nmap | Generated port scan traffic from Kali |
  | Hydra | Generated brute force traffic against Windows SMB |
