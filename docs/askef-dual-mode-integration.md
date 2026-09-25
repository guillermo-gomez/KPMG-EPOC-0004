# EPOC-0004 — ASKEF-Lite and Dual Practice Modes
Status: **IMPLEMENTATION-READY DESIGN CONTRACT, NOT DEPLOYED**.

## 1. Architecture decision

Preserve the existing execution pipeline and original KPMG K0-K13 catalog. ASKEF is metadata/governance for knowledge assets, not a replacement for React, local Whisper, the deterministic router, or the four-state interview workflow.

```text
Audio → STT → Question Router → KATA selection → Practice → Evaluation
```

Conceptual knowledge plane:

```text
                         EPOC-0004
                             |
Question ───────> Router ───> KATA
                             |
                       ASKEF metadata
                             |
              +--------------+--------------+
              A              S              K
           Alignment    Systematization Knowledge Asset
              |              |              |
              +---------- E --+-- F --------+
                        Evidence Reliability
```

ASKEF refers to the user's proposed framework: Alineación, Sistematización, Knowledge Assets, Evidencia and Fiabilidad. The ASKEF-Lite mapping below is a product-design application of that framework, not a claim that this implementation has undergone doctoral psychometric validation.

## 2. Two top-level modes

```text
INTERVIEW PRACTICE
ARCHITECTURE SIZING
```

**INTERVIEW PRACTICE:** retain existing Listening → Readiness → Answering → Summary state machine, local audio/STT, deterministic question routing and answer evaluation. Add role/JD selection, the role-specific RAI catalog and source/evidence metadata.

**ARCHITECTURE SIZING:** use scenario questions or a user-provided JD/workload, classify inputs, calculate and validate infrastructure capacity, and assess architectural reasoning. It may use the same audio/STT and answer workflow as the existing app, but its exercise state and scoring must be separate so that sizing tasks do not corrupt executive interview scoring or prior attempt records.

## 3. ASKEF metadata for every KATA

| Dimension | In EPOC-0004 |
| --- | --- |
| **A — Alignment** | Role, JD requirement, interviewer intent, competency being tested. |
| **S — Systematization** | Response method: STAR, BLUF, architecture sizing, troubleshooting; question normalization, follow-ups and rubric. |
| **K — Knowledge Asset** | KATA ID, owner, source, provenance, version, validity, technology/domain. |
| **E — Evidence** | REAL_INTERVIEW, PUBLIC_REPORTED, JD_DERIVED, own experience or benchmark; attempts, progression, coverage and verification. |
| **F — Reliability** | Grounding, no invented numbers, provenance, sensitive-data controls, abstention/escalation when evidence is insufficient. |

**Separate two taxonomies:** (a) question/source provenance = REAL_INTERVIEW / PUBLIC_REPORTED / JD_DERIVED / GENERAL_EXECUTIVE; (b) sizing variable provenance = CLIENT / RESEARCH / DESIGN / MEASURE / CALCULATE / VALIDATE. Never confuse an interview report with a benchmarked resource requirement.

### Suggested record shape

```json
{
  "kata_id": "RAI-K14",
  "title": "Ground-truth agent evaluation",
  "role_tags": ["CAIO", "AI_PRINCIPAL_SA"],
  "jd_requirements": ["RAG", "LLMOps", "Agentic AI"],
  "interviewer_intents": ["answer correctness", "ground truth"],
  "method": "Ground truth → retrieval → faithfulness → correctness → review",
  "source_provenance": "REAL_INTERVIEW",
  "owner": "Product Owner",
  "version": "1.0.0",
  "status": "DRAFT_REVIEW",
  "evidence": ["approved benchmark or transcript reference"],
  "reliability_controls": ["no invented metrics", "state limitations"],
  "acceptance_criteria": ["ground truth specified", "segmented evaluation"]
}
```

Status should transition DRAFT_REVIEW → APPROVED → DEPRECATED only upon actual review; documentation alone is not an approved or deployed asset. Personal interview evidence must be treated as user-supplied and protected; redact private names or identifiers when exporting.

## 4. Architecture Sizing Mode — mandatory reasoning flow

Prompt example:
> "We have 1,000 users, 100 active users, two requests per minute, three LLM calls per transaction and 5K tokens per call. Size the architecture."

Before solving, the application requires classification of each input:

```text
CLIENT
RESEARCH
DESIGN
MEASURE
CALCULATE
VALIDATE
```

Meanings:
- CLIENT: business-confirmed demand, SLAs and constraints.
- RESEARCH: vendor/model/SKU limits and published specifications verified for version/region.
- DESIGN: architecture choices or preliminary assumptions, always labeled.
- MEASURE: measured traces, resource counters, benchmark results.
- CALCULATE: derived value with formula, units and input references.
- VALIDATE: a decision supported by acceptance criteria and a reproducible test.

Then guide the dependency chain:

```text
Users
  → Tasks/min
  → RPM
  → TPM
  → Concurrency
  → CPU
  → RAM
  → Pods
  → Nodes
  → P99
  → TCO
```

**This is an exercise navigation chain, not a claim of automatic deductibility.** If CPU-seconds/task, memory/concurrency, average task time, quotas or model specifications are unknown, the workflow must stop at the relevant gate, ask for evidence, or request a benchmark; it must never fabricate a standard value. P99 is independently validated with an end-to-end load test. Never infer P99 from Little's Law or average throughput.

### Scenario classification for the reference case

| Variable | Value | Classification |
| --- | ---: | --- |
| Registered users | 1,000 | CLIENT |
| Active users | 100 | CLIENT, clarify if sessions or tasks |
| Requests/user/min | 2 | CLIENT or DESIGN pending traffic validation |
| LLM calls/task | 3 | DESIGN; confirm graph/traces |
| Tokens/call | 5K | DESIGN; split input vs output and measure |
| Tasks/min | 200 | CALCULATE if 100 users really produce two concurrent requests/min |
| LLM calls/min | 600 | CALCULATE |
| Token throughput | 3M tokens/min | CALCULATE using assumed 5K average |
| Average task duration | Unknown | MEASURE |
| CPU-s/task | Unknown | MEASURE |
| RAM at concurrency | Unknown | MEASURE |
| P99≤60s | Proposed objective; CLIENT approval required | CLIENT / VALIDATE |

### Formula and measurement gates

```text
tasks_per_min = active_users * tasks_per_active_user_per_min
llm_rpm = tasks_per_min * llm_calls_per_task
tpm = llm_rpm * (avg_input_tokens + avg_output_tokens)
average_inflight = (tasks_per_min / 60) * measured_mean_task_seconds
cpu_cores_observed = baseline_cores + task_rate_per_second * measured_incremental_cpu_seconds_per_task
cpu_capacity = cpu_cores_observed / selected_target_utilization
```

- CPU and sidecar resource requests require profiling under actual load.
- RAM requests require memory working-set and concurrency curves; do not extrapolate blindly.
- Pod count requires empirical per-pod concurrency while meeting the SLO; node count requires allocatable resources, system overhead, placement and failure capacity.
- If using managed Azure OpenAI, no local LLM VRAM is assigned to the Agent API/MCP pod.
- If self-hosting, calculate weights + KV cache + runtime + headroom, then **also** validate prefill/decode throughput and tail latency.
- TCO includes inference, application compute, search, data, queue/cache, network, observability and operations.
- Cost/task and cost/successful task should be separate metrics.

## 5. UX and scoring

### Start screen
Mode selector: INTERVIEW PRACTICE | ARCHITECTURE SIZING.
Role/JD selector shared by both. Original K0-K13 remain bound to KPMG_DIRECTOR_EA unless expressly selected through a compatible role mapping.

### Interview mode
Use the existing state machine; rank 2–3 role-relevant KATAS, with evidence badges. Keep live-answer guidance short and distinguish the user's personal role from team activities.

### Sizing mode
Step 1: classify all given variables by provenance.
Step 2: ask missing Discovery questions.
Step 3: choose managed vs self-hosted inference and sync vs async workflow.
Step 4: show formulas and unit checks with editable values.
Step 5: flag evidence gaps and identify which service or tool measures them.
Step 6: load-test, validate P95/P99 and dependencies, estimate TCO.
Step 7: evaluate reasoning and preserve assumption/measurement history.

**Green**: source classification, correct formulas and units, no invented constants, proper managed/self-hosted split, P99 validation plan and economic tradeoffs.
**Yellow**: conceptually correct but missing provenance, benchmark or dependency.
**Red**: invented CPU/RAM/VRAM values, treating an average as P99, using HPA to solve Azure OpenAI quota, or claiming the architecture is production-ready from a POC alone.

## 6. Implementation boundary and verification

Current repo stores documentation, not the application source. This contract does not alter the published application. Required implementation in actual source repository:
- add top-level mode selector and dedicated sizing workflow;
- preserve four-state interview behavior and device-local Whisper audio;
- add role-based routing and catalog schema migration;
- store ASKEF metadata and attempt evidence without exposing private interview content;
- add deterministic formulas and source-classification validation;
- add tests covering legacy K0–K13, routing, arithmetic, ambiguity, P99 and assumptions;
- deploy only after functional and regression verification.

**Immediate use:** existing published app may be used for its current features after microphone/STT/router smoke tests. New RAI/SIZE KATAs, ASKEF metadata and Architecture Sizing mode cannot be represented as available in the UI until implemented and validated.
