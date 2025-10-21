# Python Auto-Instrumentation (Simplified)

```mermaid
flowchart TB
  A["Pod annotated with inject-python"] --> B["OTel Operator webhook"]
  B --> C["Reads Instrumentation CR<br/>(exporter, sampler, image)"]
  C --> D["Mutates Pod:<br/>+ initContainer<br/>+ PYTHONPATH<br/>+ OTEL_* env vars"]
  D --> E["Python runtime starts<br/>→ sitecustomize.py runs"]
  E --> F["OTel SDK initialized<br/>(TracerProvider, Exporter)"]
  F --> G["Instrumentors patch libs<br/>(Flask, Requests, DB, etc.)"]
  G --> H["Spans created & exported via OTLP"]
  H --> I["Collector → Tempo / Prometheus / Loki"]
