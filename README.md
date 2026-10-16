
# Lab 27: Threat Intelligence + Endpoint Investigation

## Overview

This lab investigates how threat intelligence can support Windows endpoint investigations using Elastic Security, ES|QL, and Sysmon telemetry. The objective is to evaluate available endpoint evidence, understand the limitations of indicator correlation, and investigate gaps in event visibility without making unsupported claims about malicious activity.

The investigation encountered limited and inconsistent query results. A combined process and network query returned one document, containing Sysmon Event ID `1`, while dedicated queries for Event IDs `1` and `3` returned no results under their respective filters. The event-code distribution query returned 10 documents, including seven with a null event code.

These findings make telemetry validation a central part of the investigation.

## Objectives

- Understand how threat intelligence can guide endpoint investigations.
- Identify which indicator types can be correlated with the available telemetry.
- Examine Sysmon process-creation events and their associated fields.
- Check for network-connection events and investigate their availability.
- Validate ES|QL results before drawing conclusions.
- Document missing fields, zero-result queries, and inconsistencies in data visibility.
- Separate confirmed observations from hypotheses and unresolved questions.

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

## Conclusion

Lab 27 demonstrates the importance of validating telemetry before performing threat-intelligence correlation. Although the investigation identified one process-creation record, the available fields and inconsistent query results prevented meaningful endpoint correlation.

The next steps are to validate the Discover time range, inspect the raw document and field mappings, confirm the endpoint's Sysmon configuration, and establish whether the expected telemetry is being ingested. Threat-intelligence indicators should only be correlated after the relevant fields and data sources have been verified.

**Final assessment:** Inconclusive due to limited usable telemetry. No confirmed threat-intelligence match or endpoint compromise was established.
