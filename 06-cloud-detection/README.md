# Cloud Detection

> AWS CloudTrail investigation reports demonstrating cloud security monitoring, IAM threat detection, and CloudWatch Logs analysis. Cloud detection is now a required skill in most enterprise SOC roles.
>
> ---
>
> ## Why Cloud Detection Matters
>
> Most modern organizations run workloads in AWS, Azure, or GCP. Traditional network-based detection tools like Suricata and Zeek cannot see inside cloud environments. Cloud-native logging — specifically AWS CloudTrail — is the equivalent of a SIEM log source for cloud infrastructure.
>
> SOC analysts who can read CloudTrail logs and write CloudWatch queries are significantly more valuable than those who only know on-premises tools.
>
> ---
>
> ## AWS Services Used in This Lab
>
> | Service | Purpose |
> |---|---|
> | **AWS CloudTrail** | Records every API call made in the AWS account — who did what, when, from where |
> | **Amazon S3** | Stores the raw CloudTrail log files (.json.gz format) |
> | **Amazon CloudWatch Logs** | Streams CloudTrail events for real-time search and alerting |
> | **CloudWatch Logs Insights** | Query language for searching CloudTrail events (similar to SPL) |
> | **AWS IAM** | Identity and Access Management — the source of most high-risk cloud events |
>
> ---
>
> ## What CloudTrail Logs
>
> Every CloudTrail event record contains:
>
> | Field | Description |
> |---|---|
> | `eventName` | The API action that was performed (e.g. ConsoleLogin, CreateUser, DeleteTrail) |
> | `eventTime` | Exact timestamp of the action |
> | `userIdentity` | Who performed the action (ARN, account ID, session info) |
> | `sourceIPAddress` | The IP address the action came from |
> | `userAgent` | What tool or browser was used |
> | `responseElements` | Whether the action succeeded or failed |
> | `requestParameters` | What parameters were passed to the API call |
>
> ---
>
> ## Lab 6 — Three Simulated Threat Scenarios
>
> ### Scenario 1: Failed Console Login Brute Force
> **CloudWatch query used:**
> ```
> { $.eventName = "ConsoleLogin" && $.responseElements.ConsoleLogin = "Failure" }
> ```
> **What it detects:** Multiple failed login attempts to the AWS Management Console — a sign of credential stuffing or brute force against cloud accounts.
>
> **Result:** 5 ConsoleLogin Failure events detected matching the simulated failed logins.
>
> ---
>
> ### Scenario 2: Unauthorized IAM User Creation
> **CloudWatch query used:**
> ```
> { $.eventName = "CreateUser" }
> ```
> **What it detects:** A new IAM user being created — a common persistence technique. Attackers create backdoor accounts to maintain access even if the original compromised account is locked out.
>
> **Result:** 1 CreateUser event detected with AdministratorAccess policy attached — high severity finding.
>
> ---
>
> ### Scenario 3: Access Key Creation (Credential Staging)
> **CloudWatch query used:**
> ```
> { $.eventName = "CreateAccessKey" }
> ```
> **What it detects:** Programmatic credentials being generated. An attacker with console access creating access keys can exfiltrate those keys and use them from outside AWS indefinitely.
>
> **Result:** 1 CreateAccessKey event detected immediately after the CreateUser event — confirms credential staging sequence.
>
> ---
>
> ## Files in This Folder
>
> | File | Description |
> |---|---|
> | `lab6-aws-cloudtrail.md` | Full investigation report for all 3 threat scenarios |
> | `screenshots/` | CloudTrail console, CloudWatch query results, and raw JSON evidence |
>
> ---
>
> ## MITRE ATT&CK Coverage
>
> | Tactic | Technique | ID |
> |---|---|---|
> | Initial Access | Valid Accounts: Cloud Accounts | T1078.004 |
> | Persistence | Create Account: Cloud Account | T1136.003 |
> | Credential Access | Steal Application Access Token | T1528 |
> | Defense Evasion | Impair Defenses: Disable Cloud Logs | T1562.008 |
>
> ---
>
> ## Key Defender Recommendation
>
> **Monitor for `DeleteTrail` events immediately.**
>
> If a CloudTrail trail is deleted, all subsequent activity in that account becomes invisible to logging. This is one of the highest-priority alerts in any cloud environment. A `DeleteTrail` event should trigger a P1 incident response regardless of who performed it.
