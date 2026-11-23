# Use of Playbooks, Runbooks, and Specialized Queries
## Summary
Automation would greatly help reduce analyst workload and improve detection/response times. We will create some example KQL queries to detect common IOCs listed below, map out a general investigation process for that particular flagged event, and potential containment actions.

Example common IOCs include:
```text
failed authentacation/brute force/password spraying
new uknown users
priviledge modifications
reconnissance
```

Runbooks on the other hand assist with maintaining the investigative workflow. For example, after a vulnerability has been discovered by Nessus, we should in order:
```text
1. Validate the finding
2. Determine CVSS
3. Determine the exposure
4. Determine exploitability (for our organization)
5. Assign remediation owner and track the progress
7. Rescan to verify
8. Close the incident/ticket/case
'''
