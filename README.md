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
