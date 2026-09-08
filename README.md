
# threatflow-n8n
# ThreatFlow 

ThreatFlow is an educational cybersecurity automation project built to explore how **n8n, Docker, APIs, and JavaScript** can be combined to create a simple threat-intelligence workflow.

The project accepts an IP address as an Indicator of Compromise (IOC), enriches it using the AbuseIPDB API, evaluates the returned threat intelligence, assigns a risk level, and generates an analyst-readable report.

## Project Purpose

This project was created as a hands-on proof of concept to:

- Learn how n8n workflows are designed and executed
- Run n8n locally using Docker
- Integrate an external threat-intelligence API
- Pass data between automated workflow nodes
- Apply basic risk-classification logic using JavaScript
- Transform raw API results into useful analyst information
- Generate a downloadable threat-intelligence report
- Practice secure API credential handling in n8n

This is an educational project and is not intended to replace a production threat-intelligence or SOC platform.

## Workflow

ThreatFlow currently follows this process:

**Start ThreatFlow → IOC Input → AbuseIPDB Enrichment → Risk Assessment → Analyst Report → Convert to File**

### 1. IOC Input

A test IPv4 address is submitted to the workflow as the indicator to investigate.

### 2. Threat Intelligence Enrichment

n8n sends the IP address to the AbuseIPDB API and retrieves reputation and abuse information including:

- Abuse confidence score
- ISP
- Country
- Domain and hostname
- Tor status
- Number of abuse reports
- Number of distinct reporting users
- Last reported activity

### 3. Risk Assessment

A JavaScript Code node evaluates the AbuseIPDB confidence score and assigns a simple triage classification:

| Abuse Confidence Score | ThreatFlow Classification |
| --- | --- |
| 0–24 | LOW |
| 25–74 | MEDIUM |
| 75–100 | HIGH |

The LOW and MEDIUM ranges are ThreatFlow triage categories used for this educational workflow. A score of 75 or greater represents a high-confidence abuse result for the purposes of the workflow.

### 4. Analyst Report

ThreatFlow extracts the most relevant enrichment data and generates a readable analyst report containing the IP address, risk classification, reputation score, network information, Tor status, abuse reports, and timestamp.

### 5. Report Export

The final report is converted into a downloadable `.txt` file.

## Example Result

The initial workflow test used Google's public DNS address:

`8.8.8.8`

The workflow returned an AbuseIPDB confidence score of `0` and classified the IP as:

**LOW RISK**

The repository includes a sample generated report.

## Technology

- n8n
- Docker
- AbuseIPDB API
- JavaScript
- REST API / HTTP
- Git & GitHub

## Security

The AbuseIPDB API key is stored using **n8n Credentials** rather than being hard-coded into the workflow.

API credentials and secrets are not included in this repository.

## Future Improvements

Potential extensions include:

- Accepting IP addresses through a webhook
- Enriching indicators with multiple threat-intelligence sources
- Supporting domains, URLs, and file hashes
- Adding additional risk indicators beyond a single reputation score
- Sending alerts for high-risk indicators
- Exporting structured JSON or HTML reports
- Integrating the workflow with SIEM or incident-response tooling

## Workflow Overview

<img width="1870" height="832" alt="ThreatFlow n8n Workflow" src="https://github.com/user-attachments/assets/d0214a93-abfa-47e2-bd77-26ebe51945b6" />

**IOC → AbuseIPDB → Enrichment → Risk Classification → Analyst Report → File Export**

## Disclaimer

ThreatFlow was created for educational and portfolio purposes. Risk classifications produced by this proof of concept should not be used as the sole basis for blocking, containment, or other production security decisions.
