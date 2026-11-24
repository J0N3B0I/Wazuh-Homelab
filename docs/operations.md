# Operations and troubleshooting

## Routine health checks

Run these read-only checks inside the Wazuh VM:

```sh
systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard filebeat
sudo /var/ossec/bin/agent_control -l
free -h
df -h
sudo filebeat test output
```

Check the manager's log and recent systemd messages when a component needs investigation:

```sh
sudo tail -n 50 /var/ossec/logs/ossec.log
sudo journalctl -u wazuh-manager -n 50 --no-pager
sudo journalctl -u wazuh-indexer -n 50 --no-pager
sudo journalctl -u filebeat -n 50 --no-pager
```

Logs and connection-test output can contain private identifiers. Redact evidence before publishing it.

## Trace a missing alert

| Symptom | Next checks |
| --- | --- |
| Agent disconnected | Agent service, manager destination, permitted network path, enrollment, and time |
| Agent active but no expected event | Correct collection source, permissions, module status, and scan schedule |
| Event reaches manager but no alert appears | Decoder, rule match, correlation conditions, and alert output threshold |
| Manager alert exists but dashboard has no result | Filebeat output, indexer health, index/data view, filters, permissions, and time range |
| Dashboard unavailable | Dashboard service, certificate/browser access, indexer connection, and manager API connection |

An active connection confirms reachability at inspection. Collection and indexing still require their own checks.

## Memory

The VM runs the manager, indexer, dashboard, and supporting services. The indexer's inspected JVM settings are `-Xms1024m` and `-Xmx1024m`, which describe its configured Java heap. Total guest usage also includes other processes and kernel memory.

An earlier guest check showed approximately 3.2 GiB used, 4.4 GiB in cache, and 4.6 GiB available. Filesystem cache is reclaimable. Use guest available memory, swap activity, memory pressure, service errors, and workload peaks when evaluating resource changes.

## Disk and retention

Monitor filesystem space and index growth. This deployment has a 40 GiB virtual disk. The inspection found 49 alert indices with no assigned ISM policy. Check for any separately scheduled cleanup, choose a retention period, and verify that the resulting lifecycle policy is actually applied to the intended indices.

Keep alert indices, inventory/state indices, and operational logs in scope when planning disk usage. Choose retention according to investigation needs and available capacity. [Indexer lifecycle management](https://documentation.wazuh.com/current/user-manual/wazuh-indexer/index-life-management.html)

## Upgrades

Record current versions, review the release's supported upgrade sequence and agent compatibility, establish a recovery path, then plan a maintenance window. Check services, agent connectivity, forwarding, and indexed evidence after the change. The installed 4.14.8 components are the observed baseline for this repository. [Wazuh upgrade guide](https://documentation.wazuh.com/current/upgrade-guide/index.html)

## Backup and recovery

Recovery planning must cover manager configuration and enrollment state, custom rules and decoders, certificates, dashboard settings, and indexed data according to the selected recovery objective. Use the component-specific backup procedures and an appropriate indexer snapshot strategy.

The homelab's guest backups have been blocked by a capacity guard. Backup changes are deferred by the owner pending additional storage. A recent service health check does not establish a usable recovery point. Document the actual recovery coverage and complete an isolated restore test once storage is available. [Wazuh backup guidance](https://documentation.wazuh.com/current/user-manual/files-backup/index.html)
