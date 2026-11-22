soc Vulnerability Management
FIRST enabling advanced auditing
DC01 - group policy management 

in gpo, domain, secure.local, default domain policy -> edit
enable.. logginf success/fail of ..
Audit Credential Validation
Audit Logon
Audit Logoff
Account Management
Audit User Account Management
Audit Process Creation

gpupdate /force and creating gpo for domain to save time ....

add machines to workstations ou in ad, created gpo in workstations ou, named it auditing policy and edited it to reflect same rules above^^^ (success/fails)

powershell to update remote devices on domain:
Invoke-GPUpdate -Computer WIN01 -Force
Invoke-GPUpdate -Computer WIN02 -Force

need to enable the rules via PowerShell
 on end point before updating
Powershell:# Enable Remote Scheduled Tasks Management rules
Enable-NetFirewallRule -DisplayGroup "Remote Scheduled Tasks Management"

Enable-NetFirewallRule -DisplayGroup "Remote Service Management"
Enable-NetFirewallRule -DisplayGroup "Remote Event Log Management"
Enable-NetFirewallRule -DisplayGroup "Windows Management Instrumentation (WMI)"

3. Verify the rules
Powershell: Get-NetFirewallRule -DisplayGroup "Remote Scheduled Tasks Management" |
    Select-Object Name, Enabled
or 
pr Get-WmiObject -Class Win32_ComputerSystem -ComputerName REMOTE-PC-NAME
4. created more gpos for future... hardening and automated installations

testing auditing
entered bad passworld sevreal times on win01 as winuser1


1 n Install Nessus Essentials
2 n Perform authenticated scans
3 n Prioritize findings
4 n Remediate vulnerabilities
5 n Create vulnerability assessment report