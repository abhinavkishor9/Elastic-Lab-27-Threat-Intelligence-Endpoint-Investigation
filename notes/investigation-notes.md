
# Investigation Notes — Lab 27: Threat Intelligence + Endpoint Investigation

## 1. Investigation Objective

Determine whether the available Windows endpoint telemetry can support threat-intelligence correlation and identify the limitations preventing reliable investigation.

The investigation uses ES|QL queries against `logs-*` to examine event codes, process-related fields, and the availability of network-connection events.

## 2. Initial Telemetry Review

The initial query attempted to retrieve process-creation and network-connection events.

```esql
FROM logs-*
| WHERE event.code == "1" OR event.code == "3"
| KEEP @timestamp, host.name, event.code,
    process.name, process.executable,
    source.ip, destination.ip
| SORT @timestamp DESC
| LIMIT 50
```

**Observed result:**

- Documents processed: 1
- Event code: `1`
- Host: `desktop-9mmm37v`
- Timestamp: `2026-10-16 06:06:55.434`
- Process and network fields: Empty or unavailable in the selected result

The query returned a process-creation event, but the available fields did not provide enough context to identify the process or investigate a potential indicator match.

## 3. Event-Code Distribution

The following query was used to inspect the event codes available in the searched data.

```esql
FROM logs-*
| STATS event_count = COUNT(*) BY event.code
| SORT event_count DESC
| LIMIT 50
```

**Observed result:**

| Event code | Document count |
|---|---:|
| `2` | 1 |
| `16384` | 1 |
| `16394` | 1 |
| Null | 7 |
| **Total** | **10** |

The result indicates that the searched data contained 10 documents during this check. Seven documents did not have a populated `event.code` value.

The event-code distribution did not include Event ID `3`. However, this summary alone does not establish whether the events were missing at the source, excluded by the selected time range, or represented differently in the data.

## 4. Process Investigation

A process-creation query using Event ID `1` and selected process fields returned zero documents during a subsequent check.

A more targeted query filtering for common command interpreters and script hosts also returned zero documents.

These results were inconsistent with the earlier combined query, which returned one Event ID `1` document.

Possible explanations include a change in the available data, time-range differences, field mapping or type issues, or differences between the queries. The available evidence does not establish which explanation applies.

## 5. Network Investigation

The dedicated network-event query returned zero documents.

```esql
FROM logs-*
| WHERE event.code == "3"
| KEEP @timestamp, host.name, event.provider,
    process.name, source.ip, destination.ip,
    destination.port
| SORT @timestamp DESC
| LIMIT 50
```

No matching network records were returned during this check.

A separate query filtering on `event.provider == "Microsoft-Windows-Sysmon"` also returned zero documents. This does not independently prove that Sysmon is disabled; the provider field may be missing, mapped differently, or absent from the searched records.

## 6. Threat Intelligence Correlation

The investigation plan included validating external indicators and comparing them with endpoint telemetry.

However, the collected results did not establish a verified IOC match. The returned process record did not expose usable process context, and the dedicated network query did not return matching records.

Consequently:

- No malicious IP address or domain was confirmed in endpoint telemetry.
- No matching process hash was established.
- No process was classified as malicious based solely on its name.
- No successful compromise was confirmed.

A meaningful correlation requires a validated indicator, an appropriate endpoint field, and a matching record with sufficient context.

## 7. Assessment

**Status: Inconclusive — insufficient usable telemetry.**

The investigation identified limitations in event visibility and process-field availability. Before attempting further correlation, the analyst should verify the Discover time range, inspect the raw document, review field mappings, and validate the Sysmon and Elastic ingestion configuration.

The investigation should remain open until the missing evidence is understood or sufficient alternative telemetry becomes available.
