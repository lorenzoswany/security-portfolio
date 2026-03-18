---
title: "Case A – Authentication Abuse & Brute Force Detection"
weight: 1
---
# Case A – Authentication Abuse & Brute Force Detection
---

### MITRE ATT&CK
**Tactic:** Initial Access\
**Technique:** T1110.003 – Brute Force: Password Spraying

This project demonstrates detection and investigation of repeated failed network authentication attempts using Windows Security logs and Splunk.

---

## Scenario

A Windows host begins receiving multiple failed remote logon attempts from a single source IP within a short period of time. The objective is to determine whether the activity represents brute-force behavior, password spraying, or benign misconfiguration.

## Detection Logic

This detection targets Windows Security Event ID **4625** (failed logon) with Logon Type 3, which indicates a network-based authentication attempt such as SMB access.

The Alert below uses the following Splunk query:

```spl
index=main EventCode=4625 Logon_Type=3
| stats count by src_ip, user
| where count >= 3
```

[![Splunk Alert](/images/SplunkAlert1.png)](/images/SplunkAlert1.png)

The alert is configured to trigger when three or more failed network logons occur from a single source IP to a single account within a 60-minute window.

## Trigger Simulation

To generate failed network logons, I used a separate Windows “attacker” VM host to repeatedly attempt SMB authentication against the victim machine (192.168.30.62) using an incorrect password. These attempts targeted the victim’s administrative share, which produced Windows Security Event ID 4625 with Logon Type 3 on the victim host and recorded the attacker host as the source IP. 

[![Failed SMB authentication attempts from attacker host](/images/SplunkAttackSim2.png)](/images/SplunkAttackSim2.png)

---
## Evidence
Once the number of failed authentication attempts exceeded the configured threshold, the Splunk alert triggered and identified repeated failed logon activity originating from the attacker host.

The results below show the Windows Security events associated with the triggered alert.

[![Alert](/images/Alert1.png)](/images/Alert1.png)

## Investigation

After validating the alert, I reviewed the underlying authentication events to understand the scope of the activity. Multiple Event ID 4625 entries were observed originating from the same source host within a short period of time.

The events showed Logon Type 3, indicating a network-based authentication attempt, and the source workstation `WINDOWSATTACKER` (192.168.30.64) was consistently identified as the origin of the attempts.

I then searched for successful authentication events (Event ID 4624) associated with the same account to determine whether access was eventually gained. No successful logons were observed following the failed attempts.

## Findings

The activity consisted of repeated failed network authentication attempts originating from a single internal host (192.168.30.64) targeting the same account. All attempts generated Event ID 4625 with Logon Type 3, indicating network-based authentication failures.

No successful authentication events were observed for the targeted account, and the activity did not progress beyond repeated password failures. Based on the volume and pattern of attempts, the activity is consistent with brute-force style authentication behavior but did not result in account compromise in this case.

## Remediation & Recommendations

If this activity were observed in a production environment, the source host generating the authentication attempts should be investigated to determine whether the behavior is legitimate or indicative of compromise. If unauthorized activity is confirmed, the host could be temporarily isolated or blocked from further authentication attempts.

Account lockout policies should also be reviewed to ensure repeated failed logons result in temporary account lockouts, limiting brute-force attempts. Additional controls such as multi-factor authentication and continued monitoring for repeated authentication failures would further reduce the likelihood of successful credential abuse.

---