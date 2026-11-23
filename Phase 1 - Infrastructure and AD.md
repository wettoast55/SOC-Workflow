# Phase 1 - Network Infrastructure & Active Directory Deployment

## Step 1 - Create Isolated Security Network

Within the Proxmox host, 4 virtual machines were created with the following OSs: Windows 10 Pro and Windows 10 LTSC-N to serve as workstations, Windows Server 2022 to serve as a domain controller, and a ParrotOS machine to serve as an attacker/scanning device. Static IPs were configured for each machine and DC01's IP will be the set DNS for all machines. Hostnames are also set accordingly before connection validation. (Note: gateway temporarily left blank on devices to keep the subnet offline)

The isolated security subnet was created to:

- Separate lab infrastructure from production systems.
- Support vulnerability assessments.
- Simulate enterprise network segmentation.
- Enable security testing and monitoring.

```text
Bridge:
secure0

Subnet:
192.168.100.0/24

Workstations:
Windows 10 Pro (Win01: 192.168.100.30)
Windows 10 LTSC-N (Win02: 192.168.100.30)

Domain Controller:
Windows Server 2022 (DC01: 192.168.100.100)

Scanner/Attacker:
ParrotOS (Parrot01: 192.168.100.10)
```
| PVE VM Setup | VLAN Setup |
|----------|---------|
| <img width="700" height="350" alt="VMs Setup" src="https://github.com/user-attachments/assets/231eaa9f-f799-4b34-a198-9c193533f57f" /> | <img width="800" height="600" alt="VM vlan setup" src="https://github.com/user-attachments/assets/0f09bbfd-3f3e-42f0-b795-67ff970cc105" /> |

# Step 2 - Create domain + Install Active Directory Domain Services
On DC01, initialized the domain secure.local using Server Manager. Verifying it's creation within Powershell before beginning setup of ADDS

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```
Following AD initialization, created and validated forest for the domain then initializing OUs. This ensures administrative separation of privilages and allows for easier enterprise organization using AD to push Group Policies:
```text
Domain:
secure.local

NetBIOS:
SECURE
```

Validation:
```powershell
Get-ADDomain
```

Organizational Units created:
```text
secure.local

├── IT (for admin/scanner accounts)
├── Users
├── Workstations
└── Servers
```

# Step 3 - Create User Accounts

Created separate user accounts to be used alongside the default (Domain Administrator), enabling user authentication, security testing, and credentialed vulnerability scanning:

```text
kevin
nessus
winuser1
winuser2
```

| Domain Setup | Groups |
|----------|---------|
| <img width="1600" height="1200" alt="domain set up and creating ous" src="https://github.com/user-attachments/assets/05bdc61e-f1a8-4006-a35d-d25d4bf471a9" /> | <img width="1600" height="1200" alt="users setup and admin group in domain" src="https://github.com/user-attachments/assets/77a4f1ed-1ba8-4f86-9327-36a0ed17791d" /> |

# Step 4 - Join Endpoints To Domain and Install Sysmon

Verified that client devices have both been added to the domain and is now ready to receive Group Policy updates. Then remotely install Sysmon on endpoints from DC01. We temporarily enabled a secondary NIC on DC01 to download Sysmon from Microsoft's Sysinternals tool page. The zip was extracted to C:\Tools\Sysmon and installed with Powershell on DC01 (and remotely on the other domain devices).

PowerShell Sysmon Install and verification:

```
cd C:\Tools\Sysmon
.\Sysmon64.exe -accepteula -i
Get-Service *sysmon*
```


# Outcome

Successfully deployed a functional enterprise Active Directory environment supporting centralized authentication, auditing, endpoint management, and future security monitoring activities.
