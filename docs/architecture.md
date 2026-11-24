# Architecture and data flow

## Deployment boundary

The central services share one dedicated Proxmox VM. The Proxmox host, media LXC, Raspberry Pi, and dedicated FIM test VM each have a remote agent. The FIM endpoint was added by the completed detection project. The manager is also represented as a local source in its agent listing.

| Component | Responsibility | Operational dependency |
| --- | --- | --- |
| Agent | Collect configured logs, inventory, and endpoint assessment data | Endpoint services and connectivity to the manager |
| Manager | Receive and analyze events, apply decoders/rules, manage agents | Ruleset, queues, local state, and configured output |
| Filebeat | Forward the manager's JSON alerts | Alert files, certificates, credentials, and indexer connectivity |
| Indexer | Store alert documents and support searches | Disk capacity, index lifecycle, JVM memory, and cluster health |
| Dashboard | Present alerts and management information | Indexer queries and the manager API |

The observed agent input uses secure TCP on port 1514. The Filebeat-to-indexer connection uses TLS. Manager API access and browser access have their own authentication and permissions. [Official architecture](https://documentation.wazuh.com/current/getting-started/architecture.html)

## Alert data path

1. An endpoint produces an event or a monitoring module detects a change.
2. The agent collects it according to its local and centrally supplied configuration.
3. The manager receives the event and extracts fields through a decoder.
4. Rules classify the event and can correlate related observations.
5. Events meeting alert-output criteria are written to JSON alert output.
6. Filebeat forwards those alerts to the indexer.
7. The dashboard queries indexed documents for an administrator to investigate.

The inspected manager has JSON alert output enabled and a configured log alert level of 3. This threshold influences alert output. A document count therefore depends on collection, analysis, output settings, forwarding, and indexing. [Wazuh data analysis](https://documentation.wazuh.com/current/user-manual/ruleset/index.html)

## Inventory and vulnerability path

Endpoint inventory describes installed software and system characteristics. The inspected agents run Syscollector hourly. The manager's vulnerability module and indexer integration are enabled. This state-oriented path supports vulnerability views alongside the alert pipeline. Finding a vulnerable package requires validating the affected endpoint, installed version, and available remediation. [Vulnerability detection configuration](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/configuring-scans.html)

## Collection boundaries

The inspected Proxmox and media LXC agents monitor selected operating-system sources. A Docker application can write logs to its own directory, stdout, or a logging driver. Each desired application data source must be wired into collection deliberately.

Scheduled file monitoring detects changes when its scan runs. A real-time lab exercise requires a real-time watch on the intended test directory. The current 12-hour settings should be considered when interpreting a delay between a file change and an alert. [File integrity monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/index.html)

## Resilience boundary

A single VM is a common failure point for analysis, storage, and dashboard access. Recovery depends on usable backups, certificates, application state, and a tested restoration procedure. The indexer's green state describes current shard allocation. It does not establish backup recoverability.
