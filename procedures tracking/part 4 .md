1 n Install Nessus Essentials

installed nessus onto parrot via cli and registered free code
added nessus user account on dc01 to ad, tempoarily given local admin rights for priv scan

Enable Remote Registry On each Windows machine: PowerShell
Set-Service RemoteRegistry -StartupType Automatic
Start-Service RemoteRegistry

smb client to dc01 as nessus user

2 n Perform authenticated scans
nessus scan reveals 
3 n Prioritize findings
4 n Remediate vulnerabilities
5 n Create vulnerability assessment report