# Splunk DNS Threat Detection

![Splunk](https://img.shields.io/badge/Tool-Splunk-000000?style=flat&logo=splunk&logoColor=white)
![SPL](https://img.shields.io/badge/Language-SPL-orange)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

## Objective
Ingest DNS logs into Splunk, extract indicators of compromise, and build detection logic, dashboards, and alerts to identify suspicious DNS activity. This project simulates a Tier 1 SOC analyst workflow using a SIEM to detect potential command-and-control (C2) communication via DNS beaconing.

---

## Tools Used
- Splunk Enterprise
- DNS logs (CSV format)
- SPL (Search Processing Language)
   
    - ---
- DNS logs (CSV format)- SPL (Search Processing Language)## 1. Log Ingestion
- SPL (Search Processing Language)
    ### 2. Domain Extraction
    Used regex via SPL to extract domain names from raw DNS log entries:
    ```
    | rex field=_raw "(?<domain>[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})"
    ```
    This allowed analysis of destination domains even when not explicitly parsed as a field.

    ### 3. Suspicious Domain Detection
    Identified high-frequency DNS queries as a beaconing indicator:
    ```
    | stats count by domain
    | where count > 50
    ```
    Threshold of 50 was chosen based on baseline traffic analysis — legitimate domains rarely exceed this in short intervals without user-initiated browsing.

    ### 4. Dashboard Creation
    Built a Splunk dashboard with four panels:
    - DNS query distribution by domain
    - - Top host activity by source IP
      - - Suspicious domain analysis (count > 50)
        - - DNS activity over time (time chart)
         
          - ### 5. Alert Configuration
          - Built a scheduled SPL alert:
          - - Runs every 5 minutes
            - - Triggers when suspicious domain query count exceeds threshold
              - - Severity: Medium
                - - Designed to escalate to analyst queue for triage
                 
                  - ---

                  ## Key Findings

                  | Indicator | Detail |
                  |---|---|
                  | Suspicious Domain | `wpad.easyas123.tech` |
                  | Behavior | Repeated high-frequency DNS queries |
                  | Classification | Possible DNS beaconing / C2 communication |
                  | Detection Method | SPL threshold alerting (count > 50) |

                  ---

                  ## MITRE ATT&CK Mapping

                  | Technique | ID | Description |
                  |---|---|---|
                  | Application Layer Protocol: DNS | T1071.004 | Adversaries use DNS to communicate with C2 infrastructure to blend in with normal traffic |
                  | Dynamic Resolution | T1568 | Use of unusual or dynamically generated domains to evade static blocklists |

                  The repeated queries to `wpad.easyas123.tech` are consistent with **DNS beaconing behavior**, where malware periodically contacts a C2 server using DNS to receive instructions or exfiltrate data.

                  ---

                  ## Why This Matters
                  DNS-based C2 is a common evasion technique because:
                  - DNS traffic is rarely blocked at the perimeter
                  - - High query volume to a single domain is abnormal and detectable
                    - - Early detection prevents adversaries from establishing persistent access or exfiltrating data
                     
                      - This project demonstrates how a SIEM can be tuned to detect these patterns using threshold-based alerting and scheduled searches.
                     
                      - ---

                      ## Screenshots
                      *See /screenshots folder in repository*

                      ---

                      ## Skills Demonstrated
                      - SIEM log ingestion and indexing (Splunk)
                      - - SPL query writing and optimization
                        - - Regex-based field extraction
                          - - Threshold-based detection engineering
                            - - Dashboard and alert creation
                              - - IOC identification and classification
                                - - MITRE ATT&CK framework mapping
                                 
                                  - ---

                                  ## What I Would Investigate Next
                                  - Enrich the `wpad.easyas123.tech` domain against threat intel feeds (VirusTotal, AbuseIPDB) to confirm malicious classification
                                  - - Correlate DNS alerts with HTTP/HTTPS traffic to identify successful C2 connections
                                    - - Tune alert threshold dynamically based on per-host baselines to reduce false positives
                                      - - Implement a lookup table of known-bad domains to auto-classify future alerts
                                        - - Escalate confirmed IOCs to firewall team for DNS sinkholing
                                         
                                          - ---

                                          ## Disclaimer
                                          This project is for educational purposes only using simulated DNS log data.
