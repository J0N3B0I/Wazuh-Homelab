# Deployment process

This guide describes the process for the architecture documented here. Package versions and live findings are recorded in the validation guide. Exact original installer commands were not reconstructed from the running system.

## Prepare the infrastructure

Create a Linux VM on Proxmox and plan CPU, RAM, disk space, administrative access, and backup storage. This deployment uses 4 cores, 8 GiB RAM, and a 40 GiB virtual disk. Consult the matching Wazuh release documentation when sizing a reproduction. [Quickstart](https://documentation.wazuh.com/current/quickstart.html)

Plan addresses and permitted communication before installation. Use placeholders in public documentation and keep actual addresses and credentials in a private configuration store. Route endpoint telemetry to the manager and restrict management access to intended administrators.

## Install the central services

Use the official release-appropriate installation procedure for the manager, indexer, dashboard, and alert forwarding. This environment runs the three Wazuh central components at version 4.14.8 and uses Filebeat 7.10.2.

Retain generated credentials and certificates securely. Verify services individually and check the forwarding connection before enrolling endpoints. The documentation's current release can change, so confirm component compatibility before using current installation commands against this existing deployment. [Installation guide](https://documentation.wazuh.com/current/installation-guide/index.html)

## Enroll endpoints

Install a compatible agent on each endpoint, configure its manager destination, complete enrollment, and start the agent. The manager should show an active connection and a current last-seen time.

This lab's remote endpoints are the Proxmox host, media container, and Raspberry Pi. The manager-local source is counted separately. Check an endpoint's collection configuration as well as its connection status to establish coverage. [Agent enrollment](https://documentation.wazuh.com/current/user-manual/agent/agent-enrollment/index.html)

## Establish collection and baseline findings

Review local configuration and any centrally distributed agent configuration. Confirm the expected log sources, inventory settings, file-monitoring paths, scan intervals, and assessment policies.

Capture baseline findings before tuning. For each finding, record its origin, expected behavior, and impact. Apply narrow exclusions or rule changes only after understanding why the event was generated. A file change following a legitimate update may still be worth retaining as an audit event.

## Validate the complete path

Confirm an expected event on a dedicated test endpoint, locate its manager-side evidence, then find its indexed document in the dashboard. Record the agent, timestamp, rule, and resulting interpretation using sanitized identifiers.

Service checks, an active agent, and a successful forwarding connection test each cover a different part of the deployment. A completed detection exercise should demonstrate the event across the complete path.

## Record the deployment

Maintain an architecture diagram, version inventory, verification date, known limitations, and recovery notes. Export only sanitized evidence to GitHub. Future configuration changes should include the test used to confirm their outcome.
