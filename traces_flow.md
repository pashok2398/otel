# Infra Traces Flow (Operator + Collector + Tempo)

```mermaid
flowchart LR
  subgraph Dev["GitOps / Manifests"]
    A["Deployment/Pod annotated with inject-*"]
  end

  subgraph ControlPlane["Kubernetes Control Plane"]
    B{"OTel Operator\nMutating Webhook"}
    C["Instrumentation CR\n(namespace/default)"]
  end

  subgraph WorkloadNS["Workload Namespace"]
    D["Mutated PodSpec"]
    D1["+ initContainer copies auto-instr"]
    D2["+ env: OTEL_* and runtime flags"]
    D3["+ volume mounts / PYTHONPATH or NODE_OPTIONS"]
    E["App Pod running\n(SDK or auto-instrumentation)"]
    G["Envoy / Ingress (optional)\nproxy spans"]
  end

  subgraph Telemetry["Telemetry Pipeline"]
    H["OTel Collector\n(Deployment or DaemonSet)"]
    I["Processors: batch, attributes,\nresource, transform, sampling"]
    J["Tempo\n(distributed traces store)"]
    K["Grafana\n(traces UI)"]
  end

  A --> B
  B -->|Lookup policy| C
  C -->|Exporter endpoint, sampler,\npropagators, images| B
  B -->|Patch Pod| D
  D --> D1
  D --> D2
  D --> D3
  D --> E
  G -- "incoming requests" --> E

  %% Runtime trace path
  E -- "OTLP traces" --> H
  G -- "OTLP traces" --> H
  H --> I
  I --> J
  K --- J
