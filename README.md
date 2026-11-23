# Enterprise SOC & Vulnerability Management Lab

## Summary

This project documents the design, deployment, and operation of an enterprise-style cybersecurity environment that simulates real-world SOC functions using tools such as Microsoft Sentinel, Azure Arc, Sysmon, and Tenable Nessus Essentials. The goal is to emulate the technologies and workflows found within modern security operations.

Primary Objectives:

- Deploy and administer enterprise identity infrastructure.
- Implement centralized logging and security monitoring.
- Collect and investigate Windows Security Events.
- Perform authenticated vulnerability assessments.
- Report findings in line with proper threat detection, incident response, and vulnerability management methodologies.
<img width="1365" height="866" alt="sentinel main dash" src="https://github.com/user-attachments/assets/ecded122-d861-4ed1-a6dc-90cc2c84cc01" />

## Environment Overview

The environment was hosted on Proxmox VE:

### Infrastructure

```text
DC01.secure.local
Windows Server 2022
Domain Controller

WIN01.secure.local
Windows 10 Pro 21H2

WIN02.secure.local
Windows 10 LTSC-N

PARROT01
Parrot Security Linux
```

### Security Tooling

```text
Microsoft Sentinel
Azure Arc + Azure Monitor Agent
Sysmon
Tenable Nessus Essentials
```

### Network

```text
192.168.100.0/24
```

---


## Assessment Objective

Develop and secure a functional enterprise environment. To do this, we must validate security monitoring and data collection capability. Furthermore, enable centralized security analysis of this data so that the vulnerability management workflow can actually occur.

---

## Key Accomplishments

### Centralized Logging

Successfully onboarded "enterprise" infrastructure into Azure using Monitor Agent on endpoints and creating ingestion rules within the log analytics workspace. Validated application, system, and security log ingestion. Additionally deployed Sysmon across Windows systems to further support endpoint visibility and process monitoring.

### Vulnerability Management

Performed credentialed Nessus assessments against domain assets. Reviewed and validated discovered vulnerabilities allowing for deprecated SMB, administrative share, and Remote Registry access.

---

### Finding 1: CVE‑1999‑0524 - ICMP Timestamp Request Remote Date Disclosure

**Severity:** Low
**CVSS:** 4.0 


Vulnerable systems responded to ping timestamp requests (possible in enumerations) disclosing system time information. This gives potential attackers information on system clock and environmental configurations. The best recommendations are to disable ICMP timestamp responses if it is unecessary to operations. Otherwise limiting the output response or use of firewall rules to block external ping requests will be the best course of action.


### Finding 2: Administrative Services Exposure

The following ports were discovered to be open during the authenticated Nessus scan:

```text
TCP 135 – MSRPC
TCP 139 – NetBIOS
TCP 445 – SMB
TCP 49664 – Dynamic RPC
TCP 49666 – Dynamic RPC
```
These services are required for Active Directory functionality and administrative operations. The best course of action is to segment and use firewall policies where needed to reduce overall exposure.

---

# Incident Response Summary
## Identification

Telemetry from endpoints was successfully collected and centralized within Sysmon, Log Analytics and Sentinel.

Sources included:
- Windows Security Logs
- System Logs
- Application Logs
- Azure Arc Logs


## Detection

Monitoring/validation of security events and use of KQL queries to find anomalous entries such as:
```text
4624 Successful Logon
4625 Failed Logon
4720 User Creation
4740 Account Lockout
```

## Analysis

Confirmed security events by reviewing endpoint logs through the centralized dashboard. Validated proper data collection and successful onboarding of Azure agent.

## Containment

Recommended controls to limit vulnerabilities within the domain include restricting unnecessary network exposure, enforcing least privilege, and enhancing endpoint data collection on all endpoints.

## Recovery

All systems remained available throughout testing, should an attack occur to make systems unaccessible, proper incorporation of backup technologies such as snapshots and RAID on the infrastructure would lower potential operational downtime.

---

# Overall Recommendations

## Immediate

- Onboard any remaining devices and/or (future endpoints) into Sentinel.
- Create more verbose Sentinel analytics rules for monitoring select occurances.

## Near-Term

- Implement Sentinel watchlist to better tune and more efficiently analyze data flows
- Develop automated playbooks to decrease incident responder workload for low-level events such as phishing and DDoS attempts

## Long-Term

- Execute attack simulation exercises incorporating incident responders and victims alike
- Expand the Microsoft security environment to integrate Defender an Entra ID products
