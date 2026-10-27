# Robot Shop Availability SLO and Error Budget

## Purpose

Define a measurable reliability target for the Robot Shop capstone.
This is a proposed target; it is not evidence of measured availability.

## Service Level Indicator (SLI)

Availability is the percentage of valid synthetic HTTP probes to the
Robot Shop web endpoint that return a successful HTTP response within
five seconds.

SLI = successful probes / total valid probes * 100

The measurement will only be considered operational once the probe
collector and its Prometheus metrics have been configured and tested.

## Service Level Objective (SLO)

- Target availability: 99%
- Evaluation window: rolling 7 days
- Probe timeout: 5 seconds
- Success: HTTP 2xx response within the timeout
- Failure: non-2xx response, timeout, or connection failure

Planned maintenance must be documented separately; it must not be
silently removed from measurements.

## Error budget

For a 99% SLO, the error budget is 1% of valid probes in the evaluation
window. For example, out of 1,000 valid probes, up to 10 may fail while
meeting the target.

## Response policy

- Within budget: continue planned work and monitor reliability.
- Budget is being consumed rapidly: investigate failures and pause
  risky changes until the cause is understood.
- Budget exhausted: prioritise reliability fixes and defer non-essential
  changes until availability recovers.

## Evidence required

- Prometheus query or report showing successful and total probes.
- Grafana panel showing availability over the evaluation window.
- Tested alert for sustained probe failures.
- Incident record and follow-up actions for any drill or real incident.

## Current status

Proposed only. The probe metric, dashboard, alert and measured SLO
have not yet been verified in the live cluster.
