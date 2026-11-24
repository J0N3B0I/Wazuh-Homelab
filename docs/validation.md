# Validation record

## Inspection date

**November 24, 2026.** Results describe the deployment during this inspection. The repository explains Wazuh behavior using official documentation and identifies observations taken from this environment.

## Verified platform state

| Check | Result | Evidence method |
| --- | --- | --- |
| Manager | Active; version 4.14.8 | Package inventory and systemd |
| Indexer | Active; version 4.14.8 | Package inventory and systemd |
| Dashboard | Active; version 4.14.8 | Package inventory and systemd |
| Filebeat | Active; version 7.10.2 | Package inventory and systemd |
| Remote agents | Three active connections | Manager agent listing |
| Manager-local source | Active/local | Manager agent listing |
| Alert forwarding connection | Passed | `filebeat test output` exit code 0 |
| Indexer health | Green, one node, zero unassigned shards | Local authenticated indexer API query |
| Alert indices | 49, all reported green | Indexer API inventory |
| Indexed alert documents | 81,890 | Sum of the inspected alert-index document counts |
| Latest indexed alert checked | November 24, 2026, 22:03:00 UTC | Timestamp-sorted indexer search |
| Index-state-management policy assignment | Zero of 49 alert indices had a policy assigned | Indexer ISM explain API |

The local indexer inspection used the existing administrator certificate and CA trust on the VM. Certificate and credential contents are excluded from this repository.

## Verified collection settings

The Proxmox and media LXC agent configurations were inspected directly. Both configure:

- File integrity scans at startup and every 43,200 seconds, covering standard system paths.
- Security Configuration Assessment at startup and every 12 hours.
- Syscollector at startup and hourly.
- Journald collection, package-manager log collection, and active-response log collection.

The manager configuration also enables file monitoring, configuration assessment, JSON alert output, vulnerability detection, and its indexer connector. Its alert output threshold is configured at level 3. The indexer's inspected JVM heap minimum and maximum are each 1,024 MiB.

These are configuration observations. End-to-end validation of an individual detection requires a controlled event and its resulting indexed evidence.

## Recent alert sample

A bounded read from the end of the current manager alert file parsed **1,541 complete events**. The latest parsed timestamp was **2026-11-24 21:59:00 UTC**. Events represented four source IDs, including the manager-local source.

Observed groups included file integrity, rootcheck, package/configuration changes, and AppArmor. A single event can belong to several groups, so group counts can overlap. The sample was bounded by bytes and file position. It is not a 24-hour event total or a measurement of confirmed attacks.

The aggregate machine-readable record is available in [inspection.json](../evidence/inspection.json).

## Coverage and follow-up

- The Raspberry Pi connection was verified. Its endpoint collection configuration was not inspected during this pass.
- Application-specific Docker log coverage requires confirmation of the relevant sources and integrations.
- The initial inspection preceded the FIM project. The subsequent [completed FIM exercise](https://github.com/J0N3B0I/Wazuh-FIM-Lab) adds a fourth remote agent and supplies verified indexed detection evidence. The login-detection exercise remains planned.
- A custom rules file exists, but file presence does not establish a tested custom detection.
- None of the 49 inspected alert indices has an assigned ISM policy. Other possible cleanup mechanisms require separate verification before establishing retention coverage.
- The VM's 40 GiB disk is smaller than the quickstart's 50 GB storage reference for a small deployment with 90 days of data.
- Backup changes are deferred by the owner while additional storage is obtained. Recovery requires a verified backup and restore test.

The inspection used service checks and read-only queries. New detections, retention changes, agent reconfiguration, and service restarts are outside this documentation record.
