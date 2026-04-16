# Incident Reports

> Full end-to-end incident investigations following the PICERL framework. Each report documents a real attack simulated in my home lab, traced from initial alert through root cause analysis, containment, and defender recommendations.
>
> ---
>
> ## What Is PICERL?
>
> PICERL is the incident response methodology used by enterprise SOC teams:
>
> | Phase | What Happens |
> |---|---|
> | **P**repare | Tools, playbooks, and processes ready before an incident |
> | **I**dentify | Confirm the incident is real, not a false positive |
> | **C**ontain | Stop the spread immediately without destroying evidence |
> | **E**radicate | Remove the threat completely from the environment |
> | **R**ecover | Restore systems to a verified clean state |
> | **L**essons | Document what happened and improve detection |
>
> Every report in this folder follows all 6 phases.
>
> ---
>
> ## Reports in This Folder
>
> | File | Lab | Attack Type | Key Finding |
> |---|---|---|---|
> | `lab3-c2-reverse-shell.md` | Lab 3 | Reverse Shell / C2 | shell.exe spawning cmd.exe — Sysmon parent-child anomaly |
> | `lab4-lateral-movement-psexec.md` | Lab 4 | Lateral Movement | Event IDs 4648 + 4624 Type 3 + 7045 in sequence = PsExec confirmed |
> | `lab5-data-exfiltration.md` | Lab 5 | Data Exfiltration | FTP cleartext transfer — SSN and card number visible in Wireshark TCP stream |
>
> ---
>
> ## Key Investigative Techniques Used
>
> ### Sysmon Parent-Child Process Analysis (Lab 3)
> The most powerful endpoint forensics technique in this folder. Sysmon Event ID 1 records every process creation with its parent process. The pattern `shell.exe > cmd.exe` is the Sysmon signature of malware execution — no legitimate software spawns cmd.exe from a non-system parent.
>
> ### Windows Event ID Correlation (Lab 4)
> Lateral movement via PsExec leaves a specific sequence of 3 Event IDs that must be correlated together:
> - **4648** — Explicit credentials used (PsExec authenticating)
> - - **4624 Type 3** — Successful network logon over SMB
>   - - **7045** — PSEXESVC service installed on target
>    
>     - No single event is conclusive. All three in the same 15-second window = confirmed PsExec.
>    
>     - ### PCAP Protocol Analysis (Lab 5)
>     - tcpdump captured the FTP exfiltration live. Wireshark Follow TCP Stream revealed the complete contents of the stolen file in plaintext — including SSN and credit card numbers — proving FTP violates PCI DSS cardholder data protection requirements.
>    
>     - ---
>
> ## MITRE ATT&CK Coverage
>
> | Lab | Tactic | Technique | ID |
> |---|---|---|---|
> | Lab 3 | Execution | Command and Scripting Interpreter: Windows Command Shell | T1059.003 |
> | Lab 3 | Command and Control | Application Layer Protocol | T1071 |
> | Lab 3 | Command and Control | Non-Application Layer Protocol | T1095 |
> | Lab 4 | Lateral Movement | Remote Services: SMB/Windows Admin Shares | T1021.002 |
> | Lab 4 | Defense Evasion | Valid Accounts | T1078 |
> | Lab 4 | Persistence | Create or Modify System Process: Windows Service | T1543.003 |
> | Lab 5 | Exfiltration | Exfiltration Over Alternative Protocol | T1048 |
> | Lab 5 | Exfiltration | Exfiltration Over Unencrypted Protocol | T1048.003 |
