# Node.js Auto-Instrumentation (Simplified)

```mermaid
flowchart TB
  A["Pod annotated with inject-nodejs"] --> B["OTel Operator webhook"]
  B --> C["Reads Instrumentation CR<br/>(exporter, sampler, image)"]
  C --> D["Mutates Pod:<br/>+ initContainer<br/>+ NODE_OPTIONS='--require autoinstr.js'<br/>+ OTEL_* env vars"]
  D --> E["Node.js runtime starts<br/>→ autoinstr loader is required"]
  E --> F["OTel SDK initialized<br/>(TracerProvider, Exporter, AsyncLocalStorage)"]
  F --> G["Instrumentations patch modules<br/>(http, express, pg, redis, grpc, etc.)"]
  G --> H["Spans created & exported via OTLP"]
  H --> I["Collector → Tempo / Prometheus / Loki"]
