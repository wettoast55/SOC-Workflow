# Phase 2 - Microsoft Sentinel and Security Operations

## Step 1 - Create Audit Policies

Within the domain controller, we enable advanced auditing to begin collecting data to be sent to our future feeds. 

On DC01, enter Group Policy Management > default domain policy > edit to enable (succcess/fail):

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

## Step 3 - Verify rules creation and show detailed GPO information, create more GPOs specifically for future hardening/automation (Note: gpresult without /scope runs the command as the user instead of the machine, no user groups have any auditing policies set yet so it will not show any changes.
```cmd
pr Get-WmiObject -Class Win32_ComputerSystem -ComputerName REMOTE-PC-NAME
gpresult /scope computer /r
gpresult /r
```

#Final verification for auditing logs 
To check logs, I entered the incorrect login account for several uses and was able to confirm proper security log collection by the SIEM.

# Step 4 - Create Azure Resources

Set up Azure using a student account promotion, unfortunately I discovered that this meant the Azure products will only allow for deployment in a select few locations such as Central North US. Afterwards, we were able to initialize workspace within Azure for SIEM integration later on

```text
Resource Group:
CyberLab

Log Analytics Workspace:
secure-siem
```

# Step 5 - Enabling Microsoft Sentinel

Initialized Sentinel via the Azure dashboard and iinstalled the following components for the purpose of incident investiation and threat hunting:

```text
Windows Security Events
Microsoft Defender for Endpoint
Data Connector Agent
Window Security Events via AMA
```

# Step 3 - Deploy Azure Monitor Agent and Onboard DC01 Into Azure Arc

In the Azure Monitoring Agent dashboard, we copy-pasted a self-installing PowerShell script to install the agent onto DC01. Once added and enabled, we can verify connection to Azure by checking Arc > Machine > Create/Add Single Server and looking for the "Connected" status for DC01. Repeats steps as necessary for the other "enterprise" machines.

# Step 4 - Create Data Collection Rule (DCR)

Configured data collection source and set destination to forward logs to securelab-dcr for centralized viewing/analysis:

```text
Application Logs
System Logs
Security Logs
```

# Step 5 - Verify Data Collection

Validated DCR Associations:

```bash
az monitor data-collection rule association list-by-resource
```

Confirmed:

```text
DCR:
Succeeded

Association:
Present
```

---

# Step 7 - Validate Log Ingestion

Executed:

```bash
az monitor log-analytics query
```

Query:

```kusto
Event
| take 10
```

Confirmed:

```text
System Events
Application Events
Security Events
```

---

# Step 8 - Build KQL Queries

Created security-focused queries.

## Failed Logons

```kusto
Event
| where EventID == 4625
```

---

## Successful Logons

```kusto
Event
| where EventID == 4624
```

---

## Account Lockouts

```kusto
Event
| where EventID == 4740
```

---

## User Creation

```kusto
Event
| where EventID == 4720
```

---

# Step 9 - Validate Security Monitoring

Generated:

- Failed authentication events
- Administrative activity
- Log clear events
- Domain activity

Validated visibility through Log Analytics.

---

# Outcome

Successfully implemented centralized security monitoring using Microsoft Sentinel with Azure Arc integration and validated end-to-end log ingestion from Active Directory infrastructure.
