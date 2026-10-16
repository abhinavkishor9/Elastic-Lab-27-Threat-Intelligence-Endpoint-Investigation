
# Troubleshooting Notes — Lab 27: Threat Intelligence + Endpoint Investigation

## 1. Issue Summary

During the investigation, ES|QL queries produced inconsistent results:

- The combined query for event codes `1` and `3` returned one document.
- A dedicated query for event code `3` returned zero documents.
- A query for event code `1` with selected process fields returned zero documents during a subsequent check.
- The event-code distribution query processed 10 documents.
- A provider-filtered query returned zero documents.
- Several selected fields in the one returned process record were empty or unavailable.

These observations require further investigation before the endpoint telemetry can be used for reliable threat-intelligence correlation.

## 2. Verify the Discover Time Range

The available results may reflect different time ranges or data availability.

Actions:

1. Open Kibana Discover.
2. Confirm the selected data view is `logs-*`.
3. Set an absolute time range covering the recorded event timestamp, `2026-10-16 06:06:55.434`.
4. Confirm the Kibana time zone and compare it with the displayed timestamp.
5. Rerun the combined query and the dedicated Event ID `1` query without changing the time range.

Record the selected time range and the number of documents returned by each query.

## 3. Inspect the Raw Event Document

The initial query returned one document, but its selected process fields were empty.

Actions:

1. Rerun the combined query.
2. Open the returned document.
3. Inspect the full document rather than relying only on the selected columns.
4. Check whether the raw event contains process information under another field name.
5. Record the values of `event.code`, `event.provider`, `event.dataset`, `host.name`, `process.name`, and any available Sysmon-specific fields.

Do not assume that a field is absent from the underlying event merely because the selected column displays `-`.

## 4. Investigate Field Mappings

The provider-filtered query returned zero documents. This may indicate that the expected field is absent, differently mapped, or not populated in the available data.

Actions:

- Inspect the field names and values in the raw event.
- Verify the data types of `event.code` and `event.provider`.
- Check whether the Sysmon integration uses a dataset or provider field different from the one expected.
- Confirm whether the event is indexed in `logs-*` at all.

Do not change the query field or cast field types without first confirming the actual mapping.

## 5. Validate Sysmon Event Collection

A zero-result query for Event ID `3` does not establish that network collection is disabled.

If the expected events remain unavailable:

1. Inspect the Sysmon Operational log on the Windows endpoint.
2. Check whether network connection logging is enabled in the active Sysmon configuration.
3. Confirm whether recent Event ID `3` records exist locally.
4. If local events exist, verify the Elastic Agent integration and ingestion path.
5. If local events do not exist, investigate Sysmon configuration and event-generation conditions.

Keep local event generation separate from Elastic ingestion. An event missing from Discover may still exist locally.

## 6. Reconcile Inconsistent Query Results

The combined query returned one Event ID `1` document, while a later process-specific query returned zero documents.

To investigate:

- Use the same data view and time range for both queries.
- Run the simplest possible Event ID `1` filter.
- Add additional filters one at a time.
- Compare the document count after each change.
- Verify that field values and types match the query conditions.

This process can identify whether the discrepancy comes from the filter, selected fields, time range, or available data.

## 7. Current Troubleshooting Status

The root cause has not been established.

The available evidence supports reporting limited visibility and inconsistent query results, but it does not support claiming that Sysmon is disabled, Elastic Agent ingestion has failed, or network activity did not occur.

## 8. Next Actions

- [ ] Confirm the Discover time range.
- [ ] Inspect the raw Event ID `1` document.
- [ ] Verify the `event.code` and `event.provider` mappings.
- [ ] Check the local Sysmon Operational log for Event IDs `1` and `3`.
- [ ] Compare local events with the documents available in Elastic.
- [ ] Repeat the correlation queries after validating telemetry.
