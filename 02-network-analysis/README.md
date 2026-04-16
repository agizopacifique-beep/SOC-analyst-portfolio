# Network Analysis

There's a difference between knowing that a data transfer happened and being able to prove what was transferred. Security Onion can tell you a connection was made to port 21. Wireshark can show you the file that went across it, word for word.

Lab 5 is where that difference became concrete for me.

---

## Lab 5 — FTP Exfiltration PCAP Analysis

The setup: I created a file called `sensitive.txt` on the Windows 10 VM containing a fake SSN, a fake credit card number, and a name. Then I transferred it to Kali over FTP while tcpdump was capturing everything on port 21.

Watching it happen from the Kali side — pyftpdlib showing the connection come in, the file upload completing — you already know what happened. The interesting part is what Wireshark shows you afterward.

Opening the capture file and applying the `ftp` display filter, I could see the login sequence in plaintext: `USER anonymous`, then `PASS` blank, then `STOR sensitive.txt`. The filename of the stolen file is right there in the packet.

Switching to `ftp-data` and following the TCP stream is where it gets uncomfortable fast. The entire contents of the file — SSN, card number, name — are sitting there in the stream window in plain text. No decryption tool, no cracking anything. Just Wireshark and an unencrypted protocol doing what unencrypted protocols do.

That's the whole compliance story in one screenshot. This is why PCI DSS explicitly prohibits FTP for cardholder data.

**Report:** `wireshark-pcap-analysis.md`

---

## Wireshark Filters I Used

These are the actual filters from the investigation, not a generic list:

| Filter | What It Found |
|---|---|
| `ftp` | Login sequence — USER, PASS, STOR commands all in plaintext |
| `ftp-data` | The actual file transfer packets |
| `ip.addr == 192.168.56.30` | Isolated all traffic involving Kali |
| `tcp.port == 21` | Everything on the FTP port |

The Follow TCP Stream step is what ties it together. Right-click any `ftp-data` packet → Follow → TCP Stream, and you're reading the file as it crossed the wire.

---

## Files in This Folder

| File | Description |
|---|---|
| `wireshark-pcap-analysis.md` | Full analysis notes from the Lab 5 PCAP investigation |
| `screenshots/` | tcpdump running, Wireshark ftp filter, and the TCP stream showing file contents |

---

## Tools Used

| Tool | What I Used It For |
|---|---|
| tcpdump | Captured the FTP traffic to a .pcap file while the transfer was happening |
| Wireshark | Analyzed the capture — applied filters, followed the TCP stream |
| Zeek (via Security Onion) | conn.log showed the connection duration and total bytes transferred |
| Suricata | Fired an ET POLICY FTP alert — caught the unencrypted transfer automatically |
| pyftpdlib | Ran the FTP server on Kali that received the stolen file |
