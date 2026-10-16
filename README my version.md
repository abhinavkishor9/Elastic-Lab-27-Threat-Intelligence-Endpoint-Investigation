# Elastic-Lab-27-Threat-Intelligence-Endpoint-Investigation
## Overview
The main concept of this lab is to combine external threat intelligence with internal endpoint telemetry to determine whether an IP address, domain, file hash, or other indicator associated with a potential threat has any meaningful connection to activity on your Windows system.

Step 1 — External threat intelligence
Known IPs, domains, URLs, file hashes, reputation, source and confidence

Step 2 — Local endpoint telemetry
Elastic Security, Sysmon process events and network events

Step 3 — Indicator correlation
Compare indicators with observed events using ES|QL and timestamps

Step 4 — SOC assessment
Validate, investigate, document and recommend an appropriate response


This lab investigates how threat intelligence can support Windows endpoint investigations using Elastic Security, ES|QL, and Sysmon telemetry. The objective is to evaluate available endpoint evidence, understand the limitations of indicator correlation, and investigate gaps in event visibility without making unsupported claims about malicious activity.

The investigation encountered limited and inconsistent query results. A combined process and network query returned one document, containing Sysmon Event ID `1`, while dedicated queries for Event IDs `1` and `3` returned no results under their respective filters. The event-code distribution query returned 10 documents, including seven with a null event code.

These findings make telemetry validation a central part of the investigation.

## Lab Objectives

- Understand how threat intelligence indicators can support endpoint investigations and guide SOC analyst decisions.
- Evaluate the reliability, source, freshness, and context of threat intelligence indicators before using them in an investigation.
- Examine available Sysmon Event ID `1` records to identify process execution details and determine which fields are populated.
- Assess whether collected process hashes, executable paths, command lines, and parent-process information can support indicator correlation.
- Investigate the absence of Sysmon Event ID `3` results and determine whether network telemetry is available in the selected Elastic data view and time range.
- Validate event-code and provider-field visibility to identify potential filtering, mapping, or ingestion issues.
- Reconcile inconsistent query results by checking time ranges, field mappings, and query conditions.
- Distinguish a confirmed indicator match from suspicious behavior, an inconclusive result, or a query that returns no matching documents.
- Document observed evidence, query results, telemetry limitations, and recommended troubleshooting actions.
- Produce an evidence-based assessment without claiming malicious activity or endpoint compromise when the available data does not support that conclusion.

## Lab Scenario

A SOC analyst is tasked with investigating whether threat intelligence can help identify potentially suspicious activity on a Windows endpoint monitored through Elastic Security. The analyst must validate relevant indicators, examine available endpoint telemetry, and determine whether the collected evidence supports a meaningful correlation between external threat information and local process activity.

The investigation uses ES|QL to query the `logs-*` data view and examine Sysmon-related events. However, the initial results reveal limited telemetry visibility: a combined query returns one process-creation event, while subsequent process-specific and network-connection queries return no matching documents. Several fields in the returned process record are also empty or unavailable.

### Investigation Context

The analyst must work with the evidence that is actually available rather than assume that all expected endpoint events have been collected. The investigation focuses on:

- **Threat intelligence validation:** Review indicator sources, freshness, confidence, and relevance before using an indicator to support an investigative conclusion.
- **Process investigation:** Examine available Sysmon Event ID `1` records and determine whether process hashes, executable paths, command lines, and parent-process details can be used for correlation.
- **Network telemetry visibility:** Investigate why Sysmon Event ID `3` queries return zero documents without assuming that network activity did not occur.
- **Query validation:** Compare results across queries and check whether time ranges, field mappings, filters, or data availability explain the inconsistent results.
- **Evidence assessment:** Separate confirmed observations from hypotheses and document what additional evidence is required.

### Investigation Challenge

The available data does not provide enough usable process context to establish a threat-intelligence match. The dedicated network query also returns no matching records, limiting the ability to compare external IP addresses or domains against observed endpoint connections.

The analyst must determine whether the limitation lies in the selected data, query conditions, field mappings, or telemetry collection and ingestion. Any threat indicators used during the investigation must be validated independently; an indicator's reputation alone does not establish that the endpoint communicated with it or executed malicious content.

### Expected Outcome

Produce an investigation record containing the indicator sources reviewed, available endpoint evidence, ES|QL query results, troubleshooting findings, and outstanding evidence requirements.

The final assessment must state clearly whether a match was observed, whether suspicious activity is supported by the available evidence, and what remains unresolved. The investigation should not claim a confirmed compromise when telemetry is insufficient to support that conclusion.

## Environment

- **SIEM:** Elastic Security / Kibana Discover
- **Query language:** ES|QL
- **Endpoint:** Windows 11
- **Host observed:** `desktop-9mmm37v`
- **Endpoint telemetry:** Sysmon-related process and network events, where available
- **Data view:** `logs-*`

## Investigation Scope

The investigation focused on three areas:

1. **Process telemetry:** Examine Sysmon Event ID `1` and determine whether process names, executable paths, hashes, command lines, and parent-process information are available.
2. **Network telemetry:** Search for Sysmon Event ID `3` and determine whether network-connection records are visible.
3. **Threat intelligence correlation:** Establish whether the available endpoint fields can be compared against validated indicators.

No verified threat-intelligence indicator match was established in the collected results. The investigation therefore does not claim a confirmed infection, malicious connection, or successful compromise.

## Key Observations

- The combined query for event codes `1` and `3` returned one document.
- The returned document contained event code `1`, timestamped `2026-10-16 06:06:55.434`, for host `desktop-9mmm37v`.
- The selected process and network fields in that document were empty or displayed as unavailable.
- A query for event code `3` returned zero documents.
- The event-code distribution query processed 10 documents: one each for codes `2`, `16384`, and `16394`, and seven with a null event code.
- A query filtered on `event.provider == "Microsoft-Windows-Sysmon"` returned zero documents.
- Additional process-specific queries returned zero documents during the recorded checks.

These observations describe the returned query results, not proof that the endpoint generated no other events.

## Investigation Limitations

- The available results do not establish whether the zero-result queries were caused by time-range selection, field mappings, ingestion, filtering, or changes in the available data.
- The returned process record did not expose usable process context in the selected fields.
- Network-connection telemetry was not observed in the dedicated Event ID `3` query.
- No verified IOC list or matching indicator was documented as part of the collected results.
- The available evidence is insufficient to determine whether any threat indicator was associated with endpoint activity.

