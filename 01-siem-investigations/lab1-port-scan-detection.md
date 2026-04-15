Lab 1 Port Scan Detection  

Date: [put today’s date]  
Environment: Kali Linux, Windows 10, Security Onion  

Tools Used: nmap, Security Onion, Splunk  

---

MITRE ATT&CK  
- T1595.001 Scanning IP Blocks  
- T1046 Network Service Discovery  

---

1. Trigger  
Suricata alert triggered indicating possible port scanning activity from source IP.  

---

2. Scope  
Source IP: 192.168.56.30  
Destination IP: 192.168.56.20  
Target system: Windows 10 machine  
Multiple ports were scanned on the target host.  

---

3. Timeline  
- nmap scan started  
- alerts generated in Security Onion  
- investigation performed in Hunt tab  
- detection query executed in Splunk  

---

4. Root Cause  
Port scan initiated from Kali Linux using nmap to discover open ports and services on the target system.  

---

5. IOCs  
- Source IP: 192.168.56.30  
- Destination IP: 192.168.56.20  
- Activity: Port scanning  
- Ports: [fill after scan]  

---

6. Lab Metrics  
- Number of ports scanned: [fill later]  
- Number of alerts: [fill later]  
- Detection time: [fill later]  

---

7. Detection Logic  

index=* src_ip="192.168.56.30"
| stats dc(dest_port) as unique_ports by src_ip
| where unique_ports > 50  

This query detects port scanning behavior by counting unique ports accessed by a single source IP.

---

8. Containment Actions  
- Block source IP at firewall  
- Monitor for repeated scanning attempts  
- Restrict access to sensitive ports such as RDP (3389)  
- Apply network segmentation  

---

9. Improvements  
- Create alert for high number of unique port connections  
- Disable unnecessary open ports  
- Harden firewall rules  
- Enable continuous monitoring  

---

10. Screenshots  
- lab1-01-kali-nmap-output.png  
- lab1-02-suricata-alert.png  
- lab1-03-alert-details.png  
- lab1-04-hunt-kali.png  
- lab1-05-splunk-query.png  
- lab1-06-alert-triggered.png  
