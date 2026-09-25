# EPOC-0004 — Architecture Sizing Lab

## 1. Purpose

Train the candidate to determine solution scope from **requirements → researched service constraints → measured POC evidence → mathematical sizing → production validation**.

The lab must explicitly label every value as one of:

- **CLIENT** — supplied/confirmed by business or platform owner.
- **RESEARCH** — obtained from vendor/model/runtime documentation.
- **DESIGN** — architecture decision/hypothesis.
- **MEASURE** — obtained from profiling, telemetry, benchmark, or load test.
- **CALCULATE** — mathematically derived.
- **VALIDATE** — accepted only after load/reliability/cost testing.

Never present a DESIGN assumption as a universal constant.

## 2. Universal constants vs non-constants

### Universal / unit constants
- 60 seconds = 1 minute.
- 8 bits = 1 byte.
- 1024 MiB = 1 GiB.
- Kubernetes `1000m CPU = 1 CPU`.

### Not universal
- CPU per agent.
- RAM per LangGraph worker.
- CPU/RAM per MCP server.
- Istio sidecar CPU/RAM.
- tokens per request.
- LLM calls per task.
- acceptable HPA utilization target.
- headroom percentage.
- KV cache per model.
- requests per pod.

These must be researched, designed, measured, or calculated.

## 3. Discovery checklist

### Business/workload questions
| Variable | Source |
| --- | --- |
| Registered users | CLIENT |
| Daily active users | CLIENT / telemetry |
| Peak active users | CLIENT / telemetry |
| Requests per active user/min | CLIENT / telemetry |
| Business task types | CLIENT |
| P95/P99 latency objective | CLIENT/SRE |
| Availability SLO/SLA | CLIENT/SRE |
| RTO/RPO | CLIENT/SRE |
| Cost ceiling / task | CLIENT/Finance |
| Data sensitivity/residency | CLIENT/Security |
| Systems of record | CLIENT/Application Owners |

### Architecture questions
- Synchronous or asynchronous completion?
- One agent or deterministic workflow?
- Expected LLM calls per task?
- Expected tools/MCP calls per task?
- RAG? corpus size? update rate?
- Conversation memory?
- Write actions or read-only?
- Human approval?
- Multi-region?
- Managed Azure OpenAI or self-hosted model?

## 4. Reference workload

Use this case unless the KATA overrides it:

| Variable | Value | Classification |
| --- | ---: | --- |
| Registered users | 1,000 | CLIENT |
| Active/concurrent users | 100 | CLIENT/HYPOTHESIS TO CLARIFY |
| Requests/user/min | 2 | CLIENT/HYPOTHESIS TO VALIDATE |
| LLM calls/task | 3 | DESIGN |
| Tokens/call | 5,000 | DESIGN → MEASURE |
| P99 target | <= 60 s | CLIENT/SLO |

### Calculations

```math
Tasks/min = 100 * 2 = 200
```

```math
LLM RPM = 200 * 3 = 600
```

```math
TPM = 600 * 5,000 = 3,000,000
```

If mean end-to-end task time is **measured** at 40 s:

```math
lambda = 200/60 = 3.333 tasks/s
```

```math
L = lambda * W = 3.333 * 40 = 133.3 average tasks in-flight
```

Little's Law estimates average work-in-progress. It does **not** prove P99.

## 5. CPU sizing lab — Agentic AI

### Measurement
Use container CPU counters and completed-task counters over the same window.

```text
CPU-seconds/task =
  increase(container_cpu_usage_seconds_total)
  / increase(agent_tasks_completed_total)
```

Optional incremental CPU:

```text
Incremental CPU/task =
  (CPU-seconds under load - baseline CPU-seconds)
  / completed tasks
```

### Capacity calculation

If measured:
- task arrival rate = `lambda tasks/s`
- incremental CPU = `c CPU-s/task`
- baseline CPU = `b cores`

Then:

```math
CPU_observed = b + lambda * c
```

If engineering target utilization is `u`:

```math
CPU_capacity = CPU_observed / u
```

`u` is a DESIGN parameter, not a universal constant.

### Required evidence
- idle baseline,
- CPU under nominal load,
- CPU throttling,
- P95/P99,
- completed tasks,
- errors,
- HPA events.

## 6. RAM sizing lab — Agent/MCP

Measure:
- process/container baseline working set,
- memory at concurrency 1/5/10/20/...,
- cache/buffer growth,
- sidecar separately,
- GC behavior,
- OOM/restarts.

Conceptual model:

```math
RAM_pod =
  RAM_base
+ RAM_concurrent_workflows
+ RAM_buffers/cache
+ RAM_sidecar
+ headroom
```

Do not assume linear memory growth without measurement.

## 7. MCP sizing lab

MCP normally requires CPU/RAM, not GPU/VRAM unless a local ML model runs behind a tool.

Measure:
- tool calls/s,
- CPU-s/tool,
- memory/connections,
- payload sizes,
- downstream latency,
- downstream quotas,
- timeout/retry distribution.

```math
CPU_MCP ~= tool_calls_per_second * CPU_seconds_per_tool_call + baseline
```

Validate with load test because async I/O and connection pools alter behavior.

## 8. Managed Azure OpenAI sizing

If Azure hosts inference, do **not** size local VRAM.

Calculate/measure:
- task RPM,
- calls/task,
- input TPM,
- output TPM,
- deployment quota,
- 429 rate,
- concurrency,
- TTFT,
- end-to-end P95/P99,
- cost/successful task.

Keep application scaling separate from model quota.

## 9. Self-hosted LLM VRAM sizing

### Model weights

```math
WeightMemory = Parameters * bits_per_parameter / 8
```

### KV cache approximation

```math
KV_bytes_per_token
~= 2 * layers * kv_heads * head_dim * bytes_per_element
```

```math
KV_total
= KV_bytes_per_token * context_tokens * concurrent_sequences
```

### Total planning envelope

```math
VRAM_total
= weights + KV_cache + runtime_workspace + headroom
```

The following are RESEARCH values from the actual model/runtime:
- parameter count,
- layers,
- hidden size,
- attention heads,
- KV heads,
- dtype,
- quantization,
- context limit.

Runtime/workspace/headroom are MEASURE/DESIGN, not constants.

### Throughput constraint

Memory fit is insufficient.

```math
GPU_memory_count = ceil(VRAM_required / usable_VRAM_per_GPU)
```

```math
GPU_throughput_count = ceil(required_TPS / benchmark_TPS_per_GPU)
```

```math
GPU_required = max(GPU_memory_count, GPU_throughput_count)
```

## 10. RAG / Azure AI Search sizing

Discovery/research:
- corpus documents,
- average chunks/document,
- embedding dimensions,
- vector datatype,
- metadata/text size,
- update rate,
- query rate,
- replicas/partitions/service limits.

Raw vector payload:

```math
RawVectorBytes
= vectors * dimensions * bytes_per_dimension
```

This is not the total search index footprint; indexing structures, text, metadata and service overhead remain.

Measure:
- query latency P50/P95/P99,
- throttling,
- retrieval precision/recall,
- index freshness,
- query mix.

## 11. Queue / async sizing

Measure/calculate:
- arrival rate,
- service rate,
- queue depth,
- oldest-message age,
- processing-time distribution,
- retries,
- DLQ rate.

Use Service Bus for reliable business work; scale workers with KEDA/HPA based on backlog/age where appropriate.

## 12. Observability stack for the POC

| Need | Preferred tool |
| --- | --- |
| Instrumentation standard | OpenTelemetry |
| APM / distributed traces | Application Insights |
| AKS/app resource metrics | Azure Managed Prometheus |
| Dashboards | Azure Managed Grafana |
| Azure service metrics | Azure Monitor |
| Agent/model quality and traces | Microsoft Foundry Evaluation/Observability; Langfuse optional |
| Load generation | k6 or Locust |
| Self-hosted GPU | NVIDIA DCGM + vLLM metrics |
| Existing enterprise APM | Integrate Datadog or Dynatrace if already approved |

Rule: do not deploy overlapping APM products without a defined gap.

## 13. Required POC dashboards

1. Infrastructure: CPU, RAM, throttling, OOM, pods, nodes, HPA.
2. Agent: task success, graph-node latency, iterations, tools, tokens, cost.
3. Managed services: Azure OpenAI RPM/TPM/429, AI Search latency/QPS, Service Bus backlog.
4. Quality: correctness, faithfulness, citations, abstention/escalation.
5. FinOps: cost/task, cost/successful task, cache hit ratio, token distribution.

## 14. Load-test ladder

| Test | Goal |
| --- | --- |
| Idle baseline | Baseline CPU/RAM |
| Smoke | Verify instrumentation |
| 25% load | First resource curve |
| 50% load | Scaling behavior |
| 100% nominal | Production target |
| 150% stress | Saturation point |
| Soak | Memory leaks / stability |
| Failure injection | Retry, timeout, breaker, recovery |

## 15. Sizing KATAS

### SIZE-K01 — Discovery completeness
Given only "1,000 users", identify every missing variable before sizing.

### SIZE-K02 — RPM/TPM
Given active users, requests/user/min, calls/task, input/output tokens, calculate demand.

### SIZE-K03 — Little's Law
Calculate average in-flight work from arrival rate and measured mean response time; explain why it does not prove P99.

### SIZE-K04 — CPU-seconds/task
Derive CPU/task from Prometheus counters and convert to total vCPU capacity.

### SIZE-K05 — RAM/pod
Use measured baseline + concurrency curve + sidecar + headroom.

### SIZE-K06 — HPA
Convert measured load/concurrency into HPA metrics and explain the control-plane loop.

### SIZE-K07 — Node sizing
Convert pod requests + platform overhead + failure-zone requirement into minimum node count.

### SIZE-K08 — Managed LLM capacity
Determine RPM/TPM/quota; distinguish app pods from model service capacity.

### SIZE-K09 — Self-hosted VRAM
Calculate weights + KV + runtime and validate throughput.

### SIZE-K10 — RAG index
Estimate raw vector storage, then identify what must be measured/researched for actual index capacity.

### SIZE-K11 — Service Bus/KEDA
Determine backlog behavior and worker capacity.

### SIZE-K12 — SLO economics
Compare P95/P99, capacity, headroom and cost; explain trade-offs.

### SIZE-K13 — Observability design
Select OTel/App Insights/Prometheus/Foundry/Langfuse/k6 and state exactly what each measures.

### SIZE-K14 — TCO
Aggregate inference, compute, storage/search, observability, support and engineering operation into cost/successful business outcome.

## 16. Scoring rubric

A sizing answer is Green only when it:
- labels inputs by source class,
- does not invent constants,
- shows formulas,
- separates managed-service capacity from application capacity,
- distinguishes average from tail latency,
- states what must be benchmarked,
- names a validation test,
- ends with a production decision or next measurement.

Yellow:
- correct formula but assumptions are unlabelled,
- valid architecture with no measurement plan.

Red:
- derives CPU/RAM/VRAM directly from registered users,
- treats reference resource values as standards,
- claims P95/P99 from averages,
- scales pods to solve Azure OpenAI quota,
- estimates GPU count from VRAM alone.

## 17. Live answer template

> "I first classify the inputs: customer requirements, vendor constraints, design assumptions, and values that must be measured. I calculate an initial envelope, instrument the POC with OpenTelemetry, Application Insights and Prometheus, load-test it, and then convert measured CPU-seconds, memory, concurrency, RPM/TPM and P99 into pod, node or GPU capacity. I do not treat reference CPU/RAM values as universal constants."
