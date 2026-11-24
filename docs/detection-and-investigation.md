# Detection and investigation workflow

## Collect an observation

An observation can originate from an operating-system log, package inventory, a file-integrity scan, or a configuration assessment. Start by identifying the data source and its collection interval.

On the inspected Proxmox and media-container agents, journald and package-manager logs are configured sources. File integrity monitoring includes standard system paths, runs at startup, and repeats every 43,200 seconds. Syscollector runs hourly. Security Configuration Assessment runs at startup and every 12 hours.

## Decode and evaluate

The manager uses decoders to extract fields and rules to evaluate an event. Rules can match event characteristics and correlate multiple observations. Alert severity provides a prioritization signal that should be interpreted with the event's context. [Wazuh rules and decoders](https://documentation.wazuh.com/current/user-manual/ruleset/index.html)

A scheduled file-change finding can reflect package updates, an administrative change, or suspicious activity. Investigate the actor and change before deciding which explanation fits. File integrity monitoring tracks changes in configured paths and can supply attributes for that investigation. [File integrity monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/index.html)

Configuration assessment checks evaluate the endpoint against policy conditions. Review failed checks for applicability and operational impact before remediation. [Security Configuration Assessment](https://documentation.wazuh.com/current/user-manual/capabilities/sec-config-assessment/index.html)

## Find the indexed evidence

In the dashboard's event or threat-hunting view:

1. Select a time range that covers the expected event, including the collection interval.
2. Filter by the endpoint and relevant rule group.
3. Inspect the timestamp, rule ID, rule description, severity, source location, and event fields.
4. Compare related events before and after the observation.
5. Check whether another endpoint shows related activity.

Useful query starting points include `rule.groups:syscheck`, `rule.groups:rootcheck`, and `rule.groups:config_changed`. Field names and available views should be checked against the deployed dashboard and indexed event schema.

## Build an investigation timeline

Use this compact record for a test or incident:

| Field | What to record |
| --- | --- |
| Observation | What changed or triggered the alert |
| Time | Event time, timezone, and investigation time |
| Scope | Sanitized endpoint and affected component |
| Evidence | Rule ID, event fields, and related activity |
| Interpretation | Expected administration, suspicious behavior, or unresolved |
| Action | Remediation, additional collection, or documented tuning |
| Validation | How the result was checked afterward |

## Tune and validate

Choose a specific event pattern and explain why a change is justified. Test custom rules with `wazuh-logtest` before deploying them. Re-run the intended exercise and check both matching and ordinary activity. Keep custom rules in the supported custom rules location so vendor rules can be updated independently. [Testing rules and decoders](https://documentation.wazuh.com/current/user-manual/ruleset/testing.html)

## Close the loop

Retain enough sanitized evidence to reproduce the investigation. Update the configuration notes, detection rationale, and verification result. Automatic response requires a separate, explicit design and test because it can affect accounts, processes, and connectivity.
