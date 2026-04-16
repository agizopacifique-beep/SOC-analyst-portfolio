# Cloud Detection

The first time I opened AWS CloudTrail and started scrolling through API call logs, I thought: this is what Security Onion looks like for cloud environments. Every action anyone takes in your AWS account — logging in, creating a user, generating credentials, deleting a trail — shows up as a timestamped JSON event with the source IP, the user identity, and whether it succeeded or failed.

Lab 6 was about learning to query those logs and building a feel for what normal vs suspicious looks like in a cloud environment.

---

## What CloudTrail Actually Does

Every API call made in your AWS account gets recorded: who made it, from what IP, using what tool, at what time, and whether it worked. That's it. It's a comprehensive audit trail.

The challenge is finding signal in that volume. That's where CloudWatch Logs Insights comes in — it's a query interface that lets you filter CloudTrail events the same way you'd use SPL in Splunk or the Hunt tab in Security Onion.

---

## The Three Scenarios I Simulated

### Scenario 1: Console Login Brute Force

I simulated five failed logins to the AWS Management Console from an unauthorized account. Then searched for them in CloudWatch:

```
{ $.eventName = "ConsoleLogin" && $.responseElements.ConsoleLogin = "Failure" }
```

Five results came back. Checked the source IPs and the targeted account. In a real environment, five failed console logins in quick succession from an unrecognized IP would be an immediate escalation.

---

### Scenario 2: Unauthorized IAM User Creation

This is the persistence play. After getting into an account, an attacker creates a backdoor IAM user with admin access. That way, even if the original compromised credentials get rotated, they still have access.

```
{ $.eventName = "CreateUser" }
```

One result — the `test-user` I created, with `AdministratorAccess` policy attached. That policy assignment is what makes it high severity. Creating a user is not inherently suspicious. Creating one and immediately granting admin access is.

---

### Scenario 3: Access Key Creation — Credential Staging

The next step after creating a backdoor user is generating programmatic credentials — an access key and secret key — that can be used from anywhere outside AWS indefinitely.

```
{ $.eventName = "CreateAccessKey" }
```

One result, fired immediately after the CreateUser event. Together those two events in sequence tell a clear story: someone created a backdoor account and staged credentials for exfiltration.

---

## The Alert That Matters Most

If I had to pick one CloudTrail event to wake someone up at 3am, it would be `DeleteTrail`.

When a CloudTrail trail gets deleted, everything that happens in that AWS account from that point forward is invisible to logging. No records, no audit trail, nothing to investigate later. Attackers delete trails specifically to cover their tracks before doing something they don't want logged.

I added a high-priority CloudWatch alert for this event specifically. It should fire before anything else in a real cloud environment.

---

## Files in This Folder

| File | Description |
|---|---|
| `lab6-aws-cloudtrail.md` | Full investigation report — all three scenarios with CloudWatch queries and results |
| `screenshots/` | CloudTrail console, CloudWatch queries, and JSON event detail screenshots |

---

## Tools Used

| Tool | What I Used It For |
|---|---|
| AWS CloudTrail | Recorded every API call during the simulated attack scenarios |
| Amazon CloudWatch Logs | Stored the CloudTrail stream for real-time querying |
| CloudWatch Logs Insights | Ran the filter queries to find the three threat scenarios |
| AWS IAM | Where the simulated backdoor user and access keys were created |
| AWS Free Tier | Used throughout — the free tier is sufficient for all of this |
