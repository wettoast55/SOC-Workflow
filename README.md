# Enterprise SOC/Vulnerability Management

## Summary

This project reviews the design, deployment, and operation process of a scaled-down enterprise-style cybersecurity lab built to simulate real-world Security Operations Center processes.

The objective was to create an environment capable of supporting security investigations, assessments, logging, and threat monitoring activities comparable to an early-career SOC Analyst's duties. The intent was to further my experience with:

- Microsoft Sentinel and Azure Arc
- Active Directory Administration
- Group Policy Management
- Nessus Essentials
- Incident Response Methodology


# Environment Overview

## Infrastructure

### Hypervisor

- Self-hosted Proxmox VE

### Network

```text
Network: 192.168.100.0/24
Bridge: secure0 (separate bridge from homelab)
```

### Assets used

| Host | Role | IP |
|--------|--------|--------|
| DC01 | Domain Controller | 192.168.100.100 |
| WIN01 | Domain Workstation | 192.168.100.30 |
| WIN02 | Domain Workstation | 192.168.100.31 |
| PARROT01 | Security Testing VM | 192.168.100.10 |

---

# Phase 1: Network "Infrastructure" Deployment

## Objective

Build an enterprise Active Directory environment and isolate systems inside a secure lab network. 

---

## Network Segmentation

A dedicated security subnet was created on the Proxmox host where I run personal home-services. The isolated testing environment allows safe deployment of (less) than secure tools/packages from the internet. This segmentation also nicely mimics enterprise styled architecture and allows scaling in future projects. 

```text
secure0
192.168.100.0/24
```

---

## Active Directory Deployment

### Domain Controller

```text
Hostname:
DC01

Operating System:
Windows Server 2022
```

### Active Directory Forest

```text
Domain:
secure.local

NetBIOS:
SECURE
```

Installed Roles:

- Active Directory Domain Services
- DNS Server
- Group Policy Management

---

## Organizational Unit Structure

```text
secure.local

├── IT
├── Users
├── Workstations
└── Servers
```

---

## User Creation

Created several lab accounts:

```text
kevin
winuser1
winuser2
nessus
```

- Group Policy Processing
- Credential Validation

---

# Phase 2: Endpoint Monitoring & Security Logging

## Sysmon Deployment

Installed Sysmon on:

```text
DC01
WIN01
WIN02
```

Purpose:

- Process Creation Monitoring
- Network Connection Monitoring
- Threat Hunting
- Endpoint Visibility

Collected Events:

```text
Event ID 1
Process Creation

Event ID 3
Network Connections
```

---

## Advanced Auditing via Group Policy

Created:

```text
Security Auditing GPO
```

Linked To:

```text
Workstations OU
```


```text
Success
Failure
```

### Process Creation

```text
Success
```

### Account Management

```text
Success
Failure
```

Validation:

```cmd
gpresult /scope computer /r
```

Verified policy application.

---

# Phase 3: Microsoft Sentinel SIEM Integration

## Objective

Create centralized log collection and event monitoring platform.

---

## Azure Resources

Created:

```text
Resource Group:
CyberLab

Log Analytics Workspace:
secure-siem

Data Collection Rule:
securelab-dcr
```

---

## Azure Arc Onboarding

Successfully enrolled:

```text
DC01.secure.local
```

Validation:

```text
Status:
Connected
```

---
Succeeded
```

---

## Data Collection Rule

Configured collection of:

```text
Application Logs
System Logs
Security Logs
```

---

## Successful Log Ingestion

Validated collection from:

```text
DC01.secure.local
```

Data observed inside:

```text
Event Table
```

Examples:

```text
System Events
Application Events
Security Events
```

Example Security Event:

```text
Event ID 1102

Audit Log Cleared
```

---

## KQL Development

Example Security Queries:

### Failed Logons

```kusto
Event
| where EventLog == "Security"
| where EventID == 4625
```

### Successful Logons

```kusto
Event
| where EventID == 4624
```
PARROT01
```

Installed:

```text
Tenable Nessus Essentials
```

---

## Credentialed Scanning Configuration

Created Service Account:

```text
nessussvc
```

Configured:

- SMB Authentication
- Administrative Access
- Remote Registry

Validated:

```text
SMBv3 Authentication
IPC$
ADMIN$
```

---

## Scan Targets

```text
DC01
192.168.100.100

WIN01
192.168.100.30

WIN02
192.168.100.31
```

---

## Vulnerability Assessment Results

### Finding

```text
ICMP Timestamp Request Remote Date Disclosure
```

Severity:

```text
CVSS 4.0
Low Risk
```

- WMI
- Active Directory functions
- Remote administration

Assessment:

```text
Expected
```

---

### TCP 139

```text
NetBIOS
```

Assessment:

```text
Review necessity in production networks
```

---

### TCP 445

```text
SMB
```

Assessment:

```text
Required for Active Directory operations
```

---

### TCP 49664

```text
Dynamic RPC Port
```

Assessment:

```text
Expected
```

---

### TCP 49666

```text
Dynamic RPC Port
```

Assessment:

```text
Expected
```

---
# Incident Response Methodology

## Identification

Validated:

- Security Event Logging
- Authentication Monitoring
- Centralized Log Collection
- Endpoint Telemetry

---

## Detection

Implemented:

- Sysmon Monitoring
- Windows Security Logging
- Sentinel Log Collection
- KQL-based Investigation

---

## Analysis

Performed:

- Event Validation
- Log Correlation
- Azure Log Analytics Investigation
- Security Event Review

---

## Containment

Recommendations:

- Restrict unnecessary ICMP exposure
- Monitor exposed services
- Review SMB configuration
- Implement network segmentation controls

---
```text
DC01
```

Target:

```text
DC01
WIN01
WIN02
```

---

### Sysmon Ingestion

Add:

```text
Microsoft-Windows-Sysmon/Operational
```

to Sentinel Data Collection Rules.

Benefits:

- Process Monitoring
- Threat Hunting
- Lateral Movement Detection
- PowerShell Detection

---

### Detection Engineering

Create Analytics Rules for:

```text
4625 Failed Logons
4740 Account Lockouts
4720 User Creation
4688 Process Creation
```

---

## Medium Priority

### Sentinel Watchlists

Example:

```text
AuthorizedScanners
```

```csv
IPAddress,Description
192.168.100.10,Parrot Security Testing VM
```

---

### Sentinel Automation

Implement Playbooks:

- Incident Creation
- Email Notifications
- Automated Enrichment
- Automated Triage
- 
---

## Long-Term Roadmap

Implement:

- Microsoft Defender for Endpoint
- Microsoft Defender XDR
- Wazuh
- Attack Simulation
- Detection Engineering
- Purple Team Exercises

---

# Skills Demonstrated

- Active Directory Administration
- Windows Server Administration
- DNS Management
- Group Policy Management
- Sysmon Deployment
- Microsoft Sentinel
- Azure Arc
- Azure Monitor Agent
- Log Analytics
- KQL
- Security Monitoring
- Vulnerability Management
- Nessus Essentials
- SMB Authentication
- Incident Response
- Security Operations
- Endpoint Security
- Threat Detection

---
