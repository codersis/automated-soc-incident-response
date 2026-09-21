# Automated SOC Incident Response & Threat Enrichment Pipeline

An automated Security Operations Center (SOC) workflow that detects
suspicious authentication activity, enriches security alerts with
threat intelligence, calculates a risk score, suppresses duplicate
incidents, and creates enriched Jira incidents for analyst investigation.

---

## Project Objective

> **Detect → Enrich → Assess → Deduplicate → Create Incident**

---

## Overview

Security Operations Centers generate a large number of alerts that
often require repetitive initial investigation.

This project automates the initial investigation workflow for a
simulated brute-force authentication scenario.

Splunk is used as the SIEM and detection layer. When repeated failed
login attempts are detected, Splunk sends the alert to an n8n webhook.

n8n then orchestrates the investigation by:

1. Normalising the alert data
2. Enriching the source IP using AbuseIPDB
3. Enriching the associated file hash using VirusTotal
4. Combining the intelligence into a project-specific risk score
5. Assigning a severity level
6. Checking Jira for recent duplicate incidents
7. Creating an enriched Jira incident when no duplicate is found

The goal is to reduce repetitive SOC analyst work while preserving
the investigation context inside the incident ticket.


## Technologies

| Technology | Purpose |
|---|---|
| Splunk Enterprise | SIEM, log collection and detection |
| n8n | Workflow orchestration and automation |
| AbuseIPDB | Source IP reputation enrichment |
| VirusTotal | File/hash threat intelligence |
| Jira Cloud | Incident management |
| Docker | n8n deployment |
| PowerShell | Testing and webhook validation |
| Git / GitHub | Version control and project documentation |

## Use Case

### Simulated Brute-Force Authentication Detection

The project uses repeated failed login attempts as the initial
security detection scenario.

Example security event:

```text
event_type=failed_login
username=admin
src_ip=203.0.113.51
hostname=SOC-TEST
file_hash=44d88612fea8a8f36de82e1278abb02f
```

## 1. Splunk Detection

Splunk acts as the SIEM and detection layer.

The project detects repeated failed login attempts using SPL:

```spl
index=soc_security event_type=failed_login
| stats count by src_ip, username, file_hash
| where count >= 5
```


## 2. n8n Automation

n8n acts as the orchestration layer of the SOC pipeline.

The workflow connects Splunk detection with automated threat
intelligence enrichment, risk assessment, duplicate detection,
and Jira incident creation.

## 3. Alert Normalisation

The workflow extracts the key fields from the Splunk alert and
converts them into a consistent structure:

```text
Source IP
Username
Failed Attempts
Alert Name
File Hash
```

## 4. AbuseIPDB Enrichment

The source IP is checked using AbuseIPDB to obtain threat-intelligence
information such as:

- Abuse confidence score
- Total reports
- Usage type
- Last reported time

The enrichment results are passed to the risk-scoring stage.

## 5. VirusTotal Enrichment

The associated file hash is checked using VirusTotal to obtain
additional threat-intelligence information, including:

- Malicious detections
- Suspicious detections
- File type
- SHA-256

The extracted results are passed to the risk-scoring stage.

## 6. Risk Scoring

A project-specific heuristic risk score is calculated using multiple
security signals:

- Failed login attempts
- AbuseIPDB confidence score
- AbuseIPDB report volume
- VirusTotal malicious detections
- VirusTotal suspicious detections

The score is capped at 100 and mapped to the following severity levels:

| Risk Score | Severity |
|---|---|
| 0–39 | Low |
| 40–59 | Medium |
| 60–79 | High |
| 80–100 | Critical |

Example:

```text
Risk Score: 70 / 100
Severity: High
```

## 7. Duplicate Alert Suppression

Before creating a Jira incident, n8n checks Jira for a recent
incident associated with the source IP.

```text
Recent duplicate found → Stop
No duplicate found     → Create Jira incident
```

This prevents repeated alerts from creating duplicate tickets.

## 8. Jira Incident Creation

If no recent duplicate is found, n8n automatically creates an enriched
Jira incident containing the alert details, threat-intelligence results,
risk score, and severity.

Example:

```text
SOC Alert - High - 203.0.113.54
```

## Key Learning Outcomes

- SIEM-based threat detection using Splunk
- SOC workflow automation using n8n
- REST API and threat-intelligence integration
- IP and file-hash enrichment
- Security risk scoring and severity classification
- Alert deduplication and incident management
- Jira-based automated incident creation
- Git and GitHub project documentation

## Author

**Akanksha Gupta**
