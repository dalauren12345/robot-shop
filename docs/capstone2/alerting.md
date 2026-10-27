# Robot Shop Alerting Plan

## Purpose

Define an actionable availability alert for Robot Shop. This is a plan;
it is not evidence that a live alert or Slack notification is working.

## Planned alert

- Name: RobotShopAvailabilityProbeFailed
- Severity: warning
- Condition: the synthetic HTTP probe reports failure continuously
  for 2 minutes.
- Intended signal: `probe_success` for the Robot Shop availability probe.
- Evaluation: Prometheus, after the probe and its scrape configuration
  have been implemented and verified.
- Notification: Slack, after an approved webhook or supported integration
  has been configured securely.
- Runbook: `docs/capstone2/runbook.md`

## Required implementation

1. Configure a synthetic HTTP probe for the deployed Robot Shop web
   endpoint.
2. Verify that Prometheus receives the probe metric and that both
   successful and failed probe states can be observed.
3. Add a Prometheus alert rule using the verified metric labels.
4. Configure Alertmanager notification routing to Slack.
5. Test the alert using an approved, controlled method.
6. Verify the notification includes the alert name, severity,
   affected target and runbook reference.
7. Record the test evidence and confirm that the alert resolves after
   the probe becomes healthy again.

## Safety and secrets

- Do not put Slack webhook URLs, tokens or other secrets in Git.
- Use an approved secret-management method for notification credentials.
- Do not deliberately disrupt the live voting app to test this alert.
- Do not create a real outage just to trigger a notification; use a
  controlled test target or another approved test method.

## Current status

Planned only. A synthetic probe, alert rule, Slack route and end-to-end
notification have not yet been verified.
