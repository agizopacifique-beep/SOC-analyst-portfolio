# Detection Engineering

Most people learning SOC work focus on consuming alerts. I wanted to understand how those alerts get built in the first place. So instead of just running labs and looking at what fired, I started writing the rules myself.

This folder has two rules I wrote and tested against real traffic I generated in my own lab.

---

## Rule 1 — Wazuh Brute Force Rule (Rule ID 100001)

**File:** `wazuh-rule-100001.xml`

The scenario: Hydra is running from Kali, hammering the Windows 10 SMB service with passwords from rockyou.txt. Hundreds of Event ID 4625 (failed logon) entries are flooding into Security Onion every minute. The question is: at what point does Security Onion know this is a brute force attack and not just a user who forgot their password?

That's what this rule decides.

It watches Wazuh's base rule 18152 — which fires once per Event 4625 — and counts how many times it fires against the same target account within a 60-second window. When it hits 10, the rule fires as a Level 12 (high severity) alert.

The `same_field` parameter is the detail that matters most here. Without it, 10 different users each failing once would trigger the rule. With it, the rule only fires when one specific account is being hammered. That's the difference between a detection rule and a false positive machine.

| Field | Value | Why It Matters |
|---|---|---|
| `rule id` | 100001 | Custom rule space starts at 100000 in Wazuh |
| `if_matched_sid` | 18152 | Count fires of this base rule, not raw events |
| `frequency` | 10 | Threshold — 10 failures triggers the alert |
| `timeframe` | 60 | Counting window in seconds |
| `same_field` | targetUserName | Only count failures against the same account |
| `level` | 12 | High severity — shows up prominently in Security Onion |

I tested it by running Hydra for about 3 minutes against administrator, watching the alert fire on attempt 10, and confirming the timing in the logs. The rule fired exactly when it should.

---

## Rule 2 — Sigma Rule for PsExec Lateral Movement

**File:** `sigma-rules/psexec-lateral-movement.yml`

After running Lab 4 and watching Event 7045 fire with ServiceName: PSEXESVC, I wanted to capture that detection in a format that could run anywhere — not just in my specific Wazuh setup.

Sigma is essentially a universal language for detection rules. You write it once, then use sigma-cli to convert it to whatever SIEM syntax you need. The rule I wrote looks for Event ID 7045 with ServiceName matching PSEXESVC. That combination is essentially unique to PsExec — no other common tool installs a service with that name.

One thing worth noting: this rule will also fire for legitimate sysadmins using PsExec. I included that in the `falsepositives` section because any detection rule without documented false positives is incomplete. In a real environment you'd layer this with the source IP — if it's coming from a known admin workstation at 2pm on a Tuesday, that's different from an unknown IP at 3am.

| Field | What It Does |
|---|---|
| `logsource` | Windows Security event log |
| `detection.selection` | EventID 7045 AND ServiceName PSEXESVC |
| `level` | High |
| `tags` | attack.lateral_movement, attack.t1021.002 |
| `falsepositives` | Legitimate IT admins using PsExec for remote management |

---

## Tools Used

- Wazuh rule engine — where the XML rule lives and runs
- - Security Onion Alerts tab — where I confirmed the rule fired
  - - sigma-cli — converted the Sigma YAML to Splunk SPL automatically
    - - Splunk — saved the converted query as a detection alert
