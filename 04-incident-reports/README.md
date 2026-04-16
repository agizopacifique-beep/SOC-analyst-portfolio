# Incident Reports

These are the labs where things got interesting. Port scans and brute force are noisy — they show up instantly in the alerts tab. The three labs in this folder are different. They required actually tracing what happened, building a timeline, and understanding why the alerts fired in the order they did.

Each report follows PICERL (Prepare, Identify, Contain, Eradicate, Recover, Lessons). Not because a checklist is required, but because thinking through all six phases is what separates a real investigation from just noting that something happened.

---

## What's In Here

### Lab 3 — Reverse Shell / C2 Detection
**File:** `lab3-c2-reverse-shell.md`

This one starts with a Metasploit meterpreter session connecting from Windows back to Kali on port 4444. The alert fired in Security Onion — Suricata caught the Meterpreter traffic signature. But the interesting part was tracing it through Sysmon.

The key finding was Event ID 1 showing `shell.exe` with `ParentImage: explorer.exe`, and then immediately after, a second Event ID 1 showing `cmd.exe` with `ParentImage: shell.exe`. That second entry is the tell. `cmd.exe` does not get spawned by random executables called `shell.exe` under normal circumstances. Seeing that in the logs told me exactly what had happened and when.

From there: SHA256 hash of shell.exe, duration of the C2 connection from Zeek's conn.log, and the full process tree documented.

---

### Lab 4 — Lateral Movement via PsExec
**File:** `lab4-lateral-movement-psexec.md`

PsExec is a legitimate Microsoft tool, which is part of what makes it interesting from a detection standpoint. Running it doesn't look outright malicious — it looks like an admin doing remote work. The detection lives in the combination of three events happening in close sequence.

Event 4648 fired first — explicit credentials used. That's psexec.py authenticating with the Administrator password before connecting. Then Event 4624 Type 3 — a successful network logon over SMB. Then Event 7045 — a new service named PSEXESVC installed on the target.

Any one of those alone is explainable. All three within about 15 seconds of each other, from the same source IP, is PsExec. That's the correlation that makes the detection work.

I also used this lab to write the Sigma rule in `07-detection-engineering/` — once I knew what the evidence looked like, writing a rule to catch it automatically was the natural next step.

---

### Lab 5 — Data Exfiltration via FTP
**File:** `lab5-data-exfiltration.md`

The setup: a file called `sensitive.txt` containing a fake SSN and credit card number, transferred from Windows 10 to Kali over FTP. tcpdump was capturing the whole time.

The Wireshark part of this lab was the most striking. Using the `ftp-data` display filter and then following the TCP stream, I could read the entire contents of the file directly from the packets. The SSN and card number were right there in plaintext. No decryption, no special tools — just Wireshark and an unencrypted protocol.

That's the compliance story: FTP transfers cardholder data in cleartext, which is exactly what PCI DSS prohibits. The Wireshark screenshot from this lab is the most concrete piece of evidence in the entire portfolio.

---

## MITRE ATT&CK Tags

| Lab | Technique | ID |
|---|---|---|
| Lab 3 | Command and Scripting Interpreter: Windows Command Shell | T1059.003 |
| Lab 3 | Application Layer Protocol (C2 communication) | T1071 |
| Lab 4 | Remote Services: SMB/Windows Admin Shares | T1021.002 |
| Lab 4 | Create or Modify System Process: Windows Service | T1543.003 |
| Lab 5 | Exfiltration Over Unencrypted Protocol | T1048.003 |
