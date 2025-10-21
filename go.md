# Go Auto-Instrumentation with eBPF (Uprobes / Kprobes)

```mermaid
flowchart TB
  A["Pod annotated with inject-go"] --> B["OTel Operator webhook"]
  B --> C["Reads Instrumentation CR<br/>(exporter, sampler, image)"]
  C --> D["Mutates Pod:<br/>+ eBPF sidecar container<br/>+ required capabilities<br/>+ OTEL_* env vars"]
  D --> E["Go app starts (compiled binary, no SDK)"]
  E --> F["eBPF sidecar attaches probes to process"]
  
  subgraph P["eBPF probes attached"]
    F --> U["Uprobes → user-space hooks<br/>(net/http, gRPC functions)"]
    F --> K["Kprobes → kernel-space hooks<br/>(syscalls: send, recv, tcp_sendmsg)"]
  end
  
  U --> G["Collect HTTP/gRPC spans, timings"]
  K --> G
  G --> H["Sidecar aggregates events → OTLP export"]
  H --> I["Collector → Tempo / Prometheus / Loki"]
