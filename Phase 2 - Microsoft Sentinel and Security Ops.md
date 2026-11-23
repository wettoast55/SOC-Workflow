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

## Step 3 - Verify rules creation and show detailed GPO information, create more GPOs specifically fo future hardening/automation
```cmd
pr Get-WmiObject -Class Win32_ComputerSystem -ComputerName REMOTE-PC-NAME
gpresult /r -shows user gpo changes (none)
gpresult /scope computer /r -shows device gpo changes (yes)
```

#Final verification for auditing logs 
To check logs, I entered the incorrect login account for several uses and was able to confirm proper security log collection by the SIEM.
