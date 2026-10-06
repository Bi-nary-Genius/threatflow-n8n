# ThreatFlow

ThreatFlow is an educational cybersecurity automation project built with **n8n, Docker, APIs, and JavaScript**.

It takes an IP address as an Indicator of Compromise (IOC), looks it up in two threat-intelligence sources, **AbuseIPDB** and **Google Threat Intelligence (GTI)**, combines the results into a single risk level, and generates an analyst-readable report.

## Project Purpose

This project was built to practice:

- Building workflows in n8n
- Integrating external threat-intelligence APIs
- Assessing results with JavaScript
- Generating analyst-readable reports

## Workflow

**Start ThreatFlow → IOC Input → AbuseIPDB Enrichment → GTI IP Lookup → Risk Assessment → Analyst Report → Convert to File**

<img width="1907" height="875" alt="image" src="https://github.com/user-attachments/assets/6dc46246-225b-4f2b-8d08-e27247f06a27" />



### 1. IOC Input

You enter one IPv4 address manually. Both lookup nodes use that address automatically.

### 2. AbuseIPDB Enrichment

n8n sends the IP address to the AbuseIPDB API and retrieves community-reported abuse information, including:

- Abuse confidence score (0–100)
- ISP, country, domain, and hostname
- Usage type
- Tor status
- Number of abuse reports and distinct reporting users
- Last reported activity

### 3. GTI IP Lookup

n8n sends the same IP address to the Google Threat Intelligence (VirusTotal v3) API and retrieves:

- How many security engines rated the IP as malicious, suspicious, harmless, or undetected
- Network (AS) owner
- Threat tags

### 4. Risk Assessment

A JavaScript Code node reads both lookups, validates them, and confirms they describe the same IP before combining them. Either source can raise the risk level:

| Classification | Rule |
| --- | --- |
| HIGH | AbuseIPDB score ≥ 75, or 3+ GTI malicious flags |
| MEDIUM | If HIGH does not apply: AbuseIPDB score ≥ 25, or at least one GTI malicious or suspicious flag |
| LOW | Neither of the above |

These are project-defined thresholds. One or two malicious flags trigger MEDIUM; three or more trigger HIGH. A LOW rating does not guarantee safety.

The workflow stops with a clear error, rather than producing an unreliable verdict, if either API response is missing required data or if the two sources returned results for different IPs.

### 5. Analyst Report

ThreatFlow turns the combined data into a plain-English report covering the verdict, what each source found, who owns the IP, additional context (Tor status, last reported activity, threat tags), and the rules used to reach the verdict.

### 6. Report Export

The final report is converted into a downloadable `.txt` file.

## Example Result

Recorded test of `1.1.1.1` (Cloudflare's public DNS resolver). Live counts may differ on later runs.

- **AbuseIPDB:** confidence score 0/100, based on 93 reports from 35 distinct users
- **GTI:** 0 of 91 engines flagged it as malicious, 0 as suspicious
- **Verdict:** LOW RISK

## Technology

- n8n
- Docker
- AbuseIPDB API
- Google Threat Intelligence (VirusTotal v3) API
- JavaScript
- REST API / HTTP
- Git & GitHub

## Security

The AbuseIPDB and GTI API keys are stored using **n8n Credentials** rather than being hard-coded into the workflow.

API credentials and secrets are not included in this repository.

## Future Improvements

Potential extensions include:

- Automatic ingestion of IPs from public threat feeds
- Batch testing against known-malicious and known-clean IPs, with published results
- Accepting IP addresses through a webhook
- Supporting domains, URLs, and file hashes
- Weighted scoring that explains how much each source contributed to the verdict
- Graceful handling of private IPs, invalid input, and API rate limits
- Sending alerts for high-risk indicators
- Exporting structured JSON or STIX reports
- Mapping findings to MITRE ATT&CK
- Integrating the workflow with SIEM or incident-response tooling

## Disclaimer

ThreatFlow was created for educational and portfolio purposes and is not intended to replace a production threat-intelligence or SOC platform. Risk classifications produced by this proof of concept should not be used as the sole basis for blocking, containment, or other production security decisions.

















