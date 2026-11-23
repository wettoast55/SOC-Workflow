# Use of Playbooks, Runbooks, and Specialized Queries
Automation would greatly help reduce analyst workload and improve detection/response times. We will create some example KQL queries to detect common IOCs listed below, map out a general investigation process for that particular flagged event, and potential containment actions. With that, playbooks and runbooks go hand in hand, complementing one another to ensure all areas of the investigative process is covered. We can implement external resources such as threat feeds and public databases such as AbuseIPDB to flag potentially malicious IoC telemetry, further streamlining the response process.

## (Potentially) Automated Responses
Example common IOCs include:
```text
- Failed authentications
- Creation of new users
- User permission changes
- Abnormal DNS queries
- Unknown NMAP sweeps
```

## (Unautomated) Investigative Process
Runbooks on the other hand assist with maintaining the analytical workflow. For example, after a vulnerability has been discovered by Nessus, we should in order:
```text
1. Validate the finding
2. Determine CVSS
3. Determine the exposure
4. Determine exploitability (for our organization)
5. Assign remediation owner and track the progress
7. Rescan to verify
8. Close the incident/ticket/case
```

## Scenario examples

### 1. Failed authentications: Repeated login attempts noticed against several domain accounts
Investigative steps for these events include first identifying the target, this may be a particular user, device, web portal, etc. Next identification of the source user/device/IP address. This allows us to determine if it is an internal or external event. Specific logs to look out for include successful logons (4624), failed logons (4625), and lockout events (4740). Possible automated responses can include disabling of the affected account, forcing password reset, and blocking the source IP. An KQL snippet can include these metrics, for example, here is one that filters if there are more than five failed logons on a single account:
```KQL
Event
| where EventID == 4625
| summarize Count=count() by Account=UserName, bin(TimeGenerated,5m)
| where Count > 5
```

### 2. Creation of new users: Unexpected new user appears in the domain
Steps to take in this instance would be first determining which account spawned the new user. We can then check if this is a valid creation as enterprises generally log new employee account creations. We can also verify the account's permission if it is a valid user to maintain zero-trust and review logs for any further account creations/movement. Containment solutions can include disabling the new account, spawning account, and/or removing group memberships/user permissions. EventIDs to look out for in this situation include user creation (4720) and permission changes (4670). Here's a KQL snippet to query all logs pertaining to account creation or permission change:
```KQL
Event
| where EventID == 4720 or 4670
```

### 3. NMAP sweeps: Analyst is conducting a port scan on a development subnet
When a sweep is detected, we must first identify the source IP and determine if the scanner is authorized. It's possible it is not an attacker but an internal employee simply looking for vulnerabilities. In enterprise settings, potentially disruptive behaviors such as deep scans can affect business operations so approved maintenance windows are generally set prior to it occurring. It will be good to be aware of these set downtimes and of any preapprovals. To finish off the initial investigation into such a probe, we should also review what the targeted assets are. Should the event be deemed malicious, proper steps to prevent further malicious activity include blocking the source IP, modifying/hardening the network and targeted assets, and minimizing what kind of information a threat might be able to attain with uncredentialed scans. False positives from an internal employee should not be ignored, however it is possible to explicitly set rules to avoid these alerts. Though there are no set EventIDs to look for, we can check for anomalyous behavior by separating events into five minute windows, then checking to see which system is generating an unusually large (we'll say 50 for the example below) number of events (due to pings, queries, etc.)

```KQL
Event
| summarize EventCount=count() by Computer, bin(TimeGenerated,5m)
| where EventCount > 50
```
