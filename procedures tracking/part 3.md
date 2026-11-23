signed up for azure account trial 200$ credit

log analytics workspace 
Resource Group:
CyberLab

Workspace Name:
secure-siem

Region:
north central us (free students trial plan)

created Microsoft Sentinel added to secure siem workspace

... now in sentenial
Installed:
Windows Security Events
Microsoft Defender for Endpoint

using azure monitoring agent ..
Data Connectors to get config
Windows Security Events via AMA

downloading azure monitoring agent from onboarding process to dc01

since dc01 is on proxmox, using azure arc>machines>create/add single server

used arc to generate ps1 script to be run on dc01, then on azure verified device showed up in machines and associated dcr by enabling azure monitor agent in extension of dc01

created data collection rule for all security, sys+app logs destination set to workspace

*fixed timing for logs using external services

sentenial playbook creations and watchlists
-----------------------------
1 n Install Nessus Essentials
2 n Perform authenticated scans
3 n Prioritize findings
4 n Remediate vulnerabilities
5 n Create vulnerability assessment report