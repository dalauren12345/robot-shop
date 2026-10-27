# OpenTelemetry and Distributed Tracing Plan

## 1. Current state

Robot Shop includes existing Instana instrumentation in several services:

- **Node.js:** `cart`, `catalogue`, and `user` include the Instana collector dependency.
- **Python:** `payment` imports Instana and includes OpenTracing and Instana dependencies.
- **Java:** `shipping` includes an Instana Java SDK dependency.
- **Web frontend:** NGINX tracing is optional and depends on tracing modules being available at build time.

The monitoring configuration also defines an OpenTelemetry Collector and a Jaeger backend.

## 2. Configuration checks completed

Local Helm rendering confirmed the following:

- The OpenTelemetry Collector has an OTLP receiver configured for gRPC on port `4317` and HTTP on port `4318`.
- The Collector's rendered trace pipeline exports to Jaeger at `jaeger.monitoring.svc.cluster.local:4317`.
- Jaeger exposes OTLP gRPC on port `4317` and OTLP HTTP on port `4318`.
- Jaeger uses in-memory trace storage, so traces may be lost when its pod restarts.

These checks validate rendered configuration only. They do not demonstrate successful trace delivery.

## 3. Outstanding work

The following items remain unverified:

1. Whether the existing Instana instrumentation can export traces to the configured OpenTelemetry Collector.
2. Whether Robot Shop application services are configured to send OTLP traces to the Collector.
3. Whether a request produces a trace visible in Jaeger.
4. Whether trace context propagates across service-to-service calls.
5. Whether tracing works under generated load without causing service instability.

## 4. Implementation approach

Before changing application dependencies or Dockerfiles:

1. Review the existing Instana instrumentation and its supported export options.
2. Choose a compatible tracing approach for the current application versions.
3. Start with one service and test it locally where practical.
4. Verify that a real request generates a trace in Jaeger.
5. Expand to additional services only after the initial test succeeds.
6. Preserve existing Prometheus metrics and application functionality.
7. Record the verification steps and results for the capstone evidence.

## 5. Safety and limitations

- No live EKS deployment or monitoring installation has been performed as part of these configuration checks.
- No successful end-to-end trace has been verified yet.
- Do not replace existing instrumentation until compatibility has been established.
- Keep tracing services internal to the cluster unless a separately reviewed access requirement justifies otherwise.
- Record actual test results rather than treating successful Helm rendering as proof of runtime operation.

## 6. Completion criteria

Tracing will be considered verified only when a generated Robot Shop request produces a trace visible in Jaeger, with the expected service information and relevant request path. Evidence should include the test performed, the observed trace, and any limitations discovered.
