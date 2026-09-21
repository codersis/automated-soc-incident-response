# Splunk Alert Configuration

## Alert Name

SOC - Possible Brute Force

## Purpose

The alert detects potential brute-force authentication activity by
identifying source IP addresses associated with repeated failed login
attempts.

## Detection Query

```spl
index=soc_security event_type=failed_login
| stats count by src_ip, username, file_hash
| where count >= 5