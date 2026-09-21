# Setup Guide

This guide describes the setup required to reproduce the
Automated SOC Incident Response & Threat Enrichment Pipeline.

## 1. Prerequisites

The project uses the following tools:

- Splunk Enterprise - 10.4.3
- n8n - 2.38.7
- Docker Desktop - 4.91.0
- Jira Cloud
- AbuseIPDB account/API access
- VirusTotal account/API access
- Git and GitHub

## 2. Project Architecture

The overall workflow is:

```text
Security Logs
      ↓
    Splunk
      ↓
SPL Detection
      ↓
Scheduled Alert
      ↓
   n8n Webhook
      ↓
Alert Normalisation
      ↓
 ┌───────────────┐
 ↓               ↓
AbuseIPDB     VirusTotal
 ↓               ↓
 └───────┬───────┘
         ↓
   Risk Scoring
         ↓
 Duplicate Check
         ↓
       Jira