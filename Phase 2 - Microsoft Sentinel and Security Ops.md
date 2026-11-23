# Phase 2 - Microsoft Sentinel and Security Operations

## Step 1 - Create Audit Policies

Within the domain controller, we enable advanced auditing to begin collecting data to be sent to our future feeds. Group policy changes will be set for all device objects within the Workstation OU. 

On DC01, enter Group Policy Management > Workstation Policy > edit to enable (success/fail):

```text
Audit Credential Validation
Audit Logon
Audit Logoff
Account Management
Audit User Account Management
Audit Process Creation
```

To validate, force a Group Policy update and ensure that the workstation machines have been moved to the workstations OU:

```cmd
auditpol /get /category:*
```

## Step 2 - Remote Pushing Audit Policies
To update remote devices on the domain, create firewall rules to allow for p2p updates. 
```powershell
Enable-NetFirewallRule -DisplayGroup "Remote Scheduled Tasks Management"
Enable-NetFirewallRule -DisplayGroup "Remote Service Management"
Enable-NetFirewallRule -DisplayGroup "Remote Event Log Management"
Enable-NetFirewallRule -DisplayGroup "Windows Management Instrumentation (WMI)"
```
| Remote GPO Update Enable | Updates Pushed |
|----------|---------|
| <img width="1920" height="1028" alt="allowing remote gpo updates from dc01 part1" src="https://github.com/user-attachments/assets/3bed0af9-2891-4757-8d44-130943b8b4a1" /> |<img width="1600" height="1200" alt="allowing remote gpo updates from dc01 part2" src="https://github.com/user-attachments/assets/b5f0d3f0-0edf-42d0-94eb-f470b289ec03" /> |

## Step 3 - Verify and show created GPO information, extra group policies will be created specifically for future hardening/automation (Note: gpresult without /scope runs the command as the user instead of the machine, no user groups have any auditing policies set yet so it will not show any changes. Finally, verify logs are being generated for events selected, so in this case we purposefully enter the incorrect password on a workstation's login several times and can confirm that the corresponding events has appeared in EventViewer.
```cmd
pr Get-WmiObject -Class Win32_ComputerSystem -ComputerName REMOTE-PC-NAME
gpresult /scope computer /r
gpresult /r
```

# Step 4 - Create Azure Resources

Set up Azure using a student account promotion, unfortunately I discovered that this meant the Azure products will only allow for deployment in a select few locations such as Central North US. Afterwards, we were able to initialize workspace within the platform for the future SIEM.

```text
Resource Group:
CyberLab

Log Analytics Workspace:
secure-siem
```

# Step 5 - Enabling Microsoft Sentinel

Next we initialized Sentinel via the Azure dashboard under our resource group and additionally configured the following components for the purpose of finalizing the connection between endpoint monitoring and Azure-based incident investigation:

```text
Windows Security Events via AMA
Microsoft Defender for Endpoint
Data Connector Agent
```

# Step 6 - Deploy Azure Monitor Agent and Onboard DC01 Into Azure Arc

In the Azure Monitoring Agent dashboard, we copy-pasted a self-installing PowerShell script to install the agent onto DC01. Once added and enabled, we can verify connection to Azure by checking Arc > Machine > Create/Add Single Server and looking for the "Connected" status for DC01. Repeats steps as necessary for the other "enterprise" machines.

# Step 7 - Create and verify Data Collection Rule + Log Ingestion

Configured data collection source and set destination to forward Application, System, and Security logs to securelab-dcr for centralized viewing/analysis. After, we validate DCR using Azure cloud shell to check if the association is present:

```bash
az monitor data-collection rule association list-by-resource
```

Then we check if logs are populating from our endpoints (via cloud shell) to confirm that we are indeed receiving System, Security, and Application event updates.

```bash
az monitor log-analytics query
```

Query:

```kusto
Event
| take 10
```

| Workspace Creation | DCR |
|----------|---------|
| <img width="1354" height="880" alt="azure creation" src="https://github.com/user-attachments/assets/37cd361d-64a5-49bf-b8a9-4c4269b6e79e" /> |<img width="1388" height="843" alt="dcr setup for monitoring dc01" src="https://github.com/user-attachments/assets/c7ac91f2-bce7-427a-814d-8278eaa3702d" /> |

# Step 9 - Build KQL Queries and Validation of Security Monitoring

Lastly, to pull only relevant data, we can begin crafting targeted KQL queries for specific events in a similar fashion to simply searching up the EventID on event viewer. Instead, we can query all connected endpoints for these events. See example event queries below and corresponding IDs. We can also confirm visability of this activity in Azure's Log Analytics Dashboard.

## Failed Logons

```kusto
Event
| where EventID == 4625
```

## Account Lockouts

```kusto
Event
| where EventID == 4740
```

## User Creation

```kusto
Event
| where EventID == 4720
```

# Step 10 - Validate Security Monitoring

Generated:

- Failed authentication events
- Administrative activity
- Log clear events
- Domain activity

Validated visibility through Log Analytics.
<img width="1385" height="886" alt="working azure sent" src="https://github.com/user-attachments/assets/48d1b604-26d3-496f-b752-fcbca107612a" />


# Outcome

Successfully implemented centralized security monitoring using Microsoft Sentinel with Azure Arc and validated end-to-end log ingestion from Active Directory infrastructure.
