# Use of Playbooks, Runbooks, and Specialized Queries
Automation would greatly help reduce analyst workload and improve detection/response times. We will create some example KQL queries to detect common IOCs listed below, map out a general investigation process for that particular flagged event, and potential containment actions. With that, playbooks and runbooks go hand in hand, complementing one another to ensure all areas of the investigative process is covered.

## (Potentially) Automated Responses
Example common IOCs include:
```text
- Failed authentications
- Creation of new users
- User permission changes
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
Investigative steps for these events include first identifying the target, this may be a particular user, device, web portal, etc. Next identification of the source user/device/IP address. This allows us to determine if it is an internal or external event. Specific logs to look out for include successful logons (4624), failed logons (4625), and lockout events (4740). An KQL snippet can include these metrics, for example, here is one that filters if there are more than five failed logons on a single account:
```KQL
Event
| where EventID == 4625
| summarize Count=count() by Account=UserName, bin(TimeGenerated,5m)
| where Count > 5
```

### 2. Creation of new users: Unexpected new user appears in the domain
### 3. User permission changes: User has been added to the domain admins group
### 4. NMAP sweeps: Analyst is conducting a vulnerability assessment of a subnet
