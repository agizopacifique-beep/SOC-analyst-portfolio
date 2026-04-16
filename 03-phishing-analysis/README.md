# Phishing Analysis

Phishing is where a huge percentage of real SOC tickets come from. Someone gets a suspicious email, flags it, and a Tier 1 analyst needs to determine within minutes whether it's malicious, whether anyone else got it, and whether anyone clicked.

For Lab 7 I grabbed a real sample from PhishTank — an actual phishing email caught in the wild — and worked through it the same way I would in a real SOC environment.

---

## The Workflow

No tool tells you everything on its own. The investigation is the combination.

**Step 1 — Email headers in MXToolbox**

The first thing I do is pull the raw headers and paste them into MXToolbox. I'm looking for three things: the true sending IP (not the spoofed display name), and whether SPF, DKIM, and DMARC passed or failed.

SPF fail means the sending IP wasn't authorized to send from that domain. DKIM fail means the message signature is broken or missing. DMARC fail means the domain's own authentication policy was violated. All three failing at once is a strong signal the email is spoofed.

**Step 2 — Extract and defang every IOC**

Before I put any indicator into a search tool, I defang it. Replace `http` with `hxxp`, replace dots with `[.]`. That way if I paste the IOC into a report or email, nobody accidentally clicks it.

```
http://evil.com/steal  →  hxxp://evil[.]com/steal
192.168.1.100          →  192[.]168[.]1[.]100
```

**Step 3 — Check each indicator**

- **VirusTotal** for the sender IP and any URLs — I want the detection count out of 90 engines and any threat labels
- - **AbuseIPDB** for the IP's abuse confidence score — anything above 80% gets blocked immediately in my containment recommendation
  - - **UrlScan.io** for the URLs — it visits the phishing page in a sandbox and takes a screenshot so I can see what the victim would have seen without clicking anything myself
    - - **AlienVault OTX** for pulse count — how many threat intel reports have flagged this indicator
     
      - **Step 4 — Write the verdict**
     
      - After enrichment I write a verdict with the evidence behind it:
     
      - ```
        VERDICT: MALICIOUS
        SPF FAIL + DKIM FAIL + DMARC FAIL
        VT score: 45/90 engines flagged the sender IP
        AbuseIPDB: 87% confidence of abuse
        UrlScan: page captured showing fake Microsoft login form

        ACTIONS: Block sender domain at email gateway. Block URL at DNS/proxy.
        Check mail logs for other recipients. Check proxy logs for any clicks.
        ```

        ---

        ## Files in This Folder

        | File | Description |
        |---|---|
        | `phish-report-1.md` | Full investigation report for the PhishTank sample |
        | `screenshots/` | MXToolbox results, UrlScan verdict, VirusTotal score, AbuseIPDB score |

        ---

        ## Tools Used

        | Tool | What I Use It For |
        |---|---|
        | MXToolbox (mxtoolbox.com/EmailHeaders) | Email headers — find the real sender IP, check SPF/DKIM/DMARC |
        | UrlScan.io | Safely see what the phishing page looks like without clicking |
        | VirusTotal | IP, domain, URL — how many AV engines flag it |
        | AbuseIPDB | IP abuse confidence score and reported categories |
        | AlienVault OTX | Threat intel pulse count — is this a known threat actor infrastructure |
        | PhishTank | Where I got the real phishing sample to analyze |
