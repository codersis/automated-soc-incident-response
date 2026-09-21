# SOC Incident Response Architecture

```mermaid
flowchart LR

    A[Security Log] --> B[Splunk SIEM]

    B --> C[SPL Detection]

    C --> D[Splunk Scheduled Alert]

    D --> E[n8n Webhook]

    E --> F[Normalise Alert]

    F --> G[AbuseIPDB]

    F --> H[VirusTotal]

    G --> I[Threat Intelligence]
    H --> I

    I --> J[Risk Scoring]

    J --> K[Duplicate Check]

    K -->|No recent incident| L[Jira Incident]

    K -->|Duplicate found| M[Stop]

    L --> N[SOC Analyst]