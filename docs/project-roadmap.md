# Security project roadmap

These exercises are proposed extensions to the monitored homelab. Complete each in a dedicated test guest and record its actual results before presenting it as a finished project.

## Project 1 — File integrity detection

**Objective** — Follow a controlled file change from an endpoint to an indexed alert and an investigation note.

1. Create a small Linux test guest and enroll its Wazuh agent.
2. Create a dedicated test directory.
3. Add that directory to the agent's existing file-integrity configuration with the desired scan mode.
4. Validate the agent configuration and apply it to the test guest.
5. Establish a baseline, then create, modify, and delete a test file.
6. Find the resulting events and record the path, change type, rule, and timestamps.
7. Explain the detection delay using the configured scan mode.

**Completion evidence** — Configuration excerpt, sanitized alert evidence, a timeline, and the observed detection delay. Confirm all three intended change operations were detected. [Official FIM exercise](https://documentation.wazuh.com/current/proof-of-concept-guide/poc-file-integrity-monitoring.html)

## Project 2 — Failed-login investigation

**Objective** — Identify a controlled pattern of failed authentication attempts against the test guest.

Use a lab account and a bounded test against the dedicated guest. Confirm which authentication log the agent collects, identify the resulting rules and correlation behavior, and distinguish an isolated failure from a repeated pattern.

**Completion evidence** — Test scope, event timeline, relevant rule IDs, and an explanation of the resulting alert. [Official authentication exercise](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-brute-force-attack.html)

## Project 3 — Configuration assessment and remediation

**Objective** — Improve one applicable security configuration finding and demonstrate the result.

Record the original assessment, select a finding that applies to the test guest, explain the operational impact of the proposed fix, apply it, and confirm the next assessment reflects the new state.

**Completion evidence** — Before/after assessment, remediation rationale, configuration change, and verification. [SCA workflow](https://documentation.wazuh.com/current/user-manual/capabilities/sec-config-assessment/how-it-works.html)

## Project 4 — Incident investigation report

**Objective** — Combine the lab's authentication and file-change evidence into an investigation.

Write a timeline, identify affected components, separate verified observations from hypotheses, choose a response, and describe what further collection would improve confidence. Keep test scenarios clearly identified as simulations.

**Completion evidence** — A short report with scope, timeline, evidence, interpretation, response, and validation.

## Portfolio publication

For each completed project, include the question being tested, architecture, relevant configuration, test procedure, observed results, interpretation, and limitations. Sanitize personal identifiers, addresses, usernames, credentials, and raw event details before publishing screenshots or examples.
