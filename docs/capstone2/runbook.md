# Robot Shop Incident Runbook

## Purpose

Guide an operator through investigating and responding to Robot Shop
availability alerts without disrupting unrelated applications.

## Safety rules

- Confirm the active Kubernetes context before any cluster operation.
- Do not modify the assets-liabilities voting application.
- Do not delete pods, PVCs, or other resources as a first response.
- Do not change AWS resources without approval.
- Record timestamps, observations, commands and outcomes.

## 1. Confirm the incident

1. Check the alert name, firing time and affected service.
2. Open the Grafana dashboard and review availability and error trends.
3. Confirm whether the issue affects Robot Shop or another service.
4. Check the application URL or synthetic probe result.

## 2. Inspect the workload

After Robot Shop has been deployed and the namespace is confirmed:

    kubectl get pods -n robot-shop
    kubectl get deployments,services -n robot-shop
    kubectl get events -n robot-shop --sort-by=.lastTimestamp

Use the actual deployment namespace if it differs from robot-shop.

## 3. Investigate unhealthy pods

For an affected pod:

    kubectl describe pod POD_NAME -n robot-shop
    kubectl logs POD_NAME -n robot-shop --tail=100

Replace POD_NAME with the actual pod name. Inspect previous-container
logs only when a container has restarted and previous logs are available.

Look for image-pull failures, failed probes, insufficient resources,
application errors and dependency connection failures.

## 4. Check dependencies and capacity

- Check whether the affected service's dependencies are healthy.
- Review recent events for scheduling failures or memory pressure.
- Review Prometheus metrics and logs when those systems are available.
- Do not assume missing metrics mean the application is healthy.

## 5. Mitigate carefully

1. Identify the most likely cause using evidence.
2. Prefer a reversible, narrowly scoped change.
3. Obtain approval before changing live resources.
4. Do not restart or modify the voting app to troubleshoot Robot Shop.
5. Verify application health and alert recovery after an approved change.

## 6. Close and document

Record the start and end times, user-visible impact, evidence, root cause
(if known), mitigation, verification results and follow-up actions.
Create a blameless postmortem for the incident drill.

## Current status

Documentation prepared. Alerts, dashboards and live procedures must be
tested after the monitoring stack is deployed.

## Capstone 2 monitoring verification status

Last checked: 27 October 2026.

- Prometheus and Grafana are running in the monitoring namespace.
- The `RobotShopWebUnavailable` Prometheus alert rule is installed and loaded. Alert firing and notification delivery have not been verified.
- The Loki service is running and its readiness endpoint responds successfully.
- Fluent Bit is not fully operational: only 2 of 6 DaemonSet pods were running at the last check. Loki rejected some older log entries with HTTP 400 (`entry too far behind`), and Robot Shop log ingestion has not been confirmed.
- Some monitoring pods remain Pending because of node-affinity constraints and node pod capacity.
- The Robot Shop application pods were running at the last check. Recheck application health before making monitoring or infrastructure changes.
- Distributed tracing and end-to-end Slack alert delivery have not been verified.

### Follow-up actions

1. Investigate why Pending Fluent Bit pods have node affinity that is absent from the DaemonSet template.
2. Review Loki timestamp acceptance and Fluent Bit log ingestion.
3. Verify alert firing and notification delivery.
4. Verify end-to-end distributed tracing.
