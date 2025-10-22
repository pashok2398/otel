# Simplified OpenTelemetry Flow (Operator + Collector + Tempo)

```mermaid
flowchart LR
  A["Application Pod<br/>(Python / Node.js / Go / Java)"] -->|OTLP traces, metrics, logs| B["OpenTelemetry Collector"]
  B -->|Process & enrich data| C["Grafana Tempo<br/>(Traces)"]
  B -->|Export metrics| D["Prometheus"]
  B -->|Export logs| E["Loki"]

  subgraph Operator["OpenTelemetry Operator (Kubernetes)"]
    O1["Instrumentation CRD<br/>(defines exporter, sampler, env)"]
    O2["Mutating Webhook<br/>injects auto-instrumentation into Pods"]
    O1 --> O2
  end

  O2 --> A

  subgraph Visualization["Visualization / Observability"]
    C --> F["Grafana UI<br/>(Trace viewer)"]
    D --> F
    E --> F
  end
