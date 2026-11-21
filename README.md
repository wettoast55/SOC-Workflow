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
2. segmented network
2. renamed devices accordingly
3. created domain and enabled roles on dc ... 
4. segmented network and addded all devices to new secure0 vlan 192.168.100.1/24 vs 192.168.50.1
5.configure dstatuc addresses on all...
192.168.100.100
255.255.255.0
 
Gateway: blank
DNS: 192.168.100.100
WIN01
Plain Text
192.168.100.30
255.255.255.0
 
Gateway: blank
DNS: 192.168.100.100
WIN02
Plain Text
192.168.100.31
255.255.255.0
 
Gateway: blank
DNS: 192.168.100.100
PARROT
Plain Text
192.168.100.10
255.255.255.0
 
Gateway: blank
DNS: 192.168.100.100

--------------
DC01         192.168.100.100
WIN01        192.168.100.30
WIN02        192.168.100.31
PARROT01     192.168.100.10

Domain:
secure.local

created domain on dc01
verified it
Get-ADDomain

created ou, users, admin
---------------
4. workstations joined domain

5. installed sysmon on dc and clients
temp enabled second nic to normal network on dc
Then Install Sysmon on DC01

Download Sysmon from Microsoft Sysinternals.

Create:C:\Tools\Sysmon

Sysmon64.exe
sysmonconfig-export.xml (SwiftOnSecurity config)

Then install: PowerShell
cd C:\Tools\Sysmon
 
.\Sysmon64.exe -accepteula -i sysmonconfig-export.xml

Verify:PowerShell
Get-Service *sysmon*

deployed on dc01, win01, win02