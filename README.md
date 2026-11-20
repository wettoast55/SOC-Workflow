# SOC-Workflow
...
Using Proxmox host, initiated OSs on separate VMs
ParrotOS
Linux Workstation - Fedora
Windows Workstation - Windows 10 Pro
Windows Server - Windows Server 2022


-------------------
VM 1 - Domain Controller

Windows Server 2022

Hostname: DC01 -----
Roles:
Active Directory
DNS
DHCP (optional)
VM 2 - Workstation

Windows 11

Hostname: HR-PC01
Join domain
VM 3 - Workstation

Windows 11

Hostname: ENG-PC01
Join domain
VM 4 - Attacker Machine

Kali Linux
---------------------
1. initialized all vms
2. renamed devices accordingly
3. created domain and enabled roles on dc ... domain is LAB
4. workstations joined domain
5. installed sysmon on dc and clients
