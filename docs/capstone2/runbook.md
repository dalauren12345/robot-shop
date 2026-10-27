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
