# EPOC-0004 — Role-Based AI / Architecture KATA Pack

## 1. Governance and provenance

This pack extends EPOC-0004 without altering the original K0-K13 catalog.

- Original K0-K13 provenance: `KPMG_DIRECTOR_EA`.
- New role pack namespace: `RAI-Kxx` (Role-based AI / Architecture).
- Router eligibility: YES.
- Interview Preparation mode: YES.
- Recommended routing output: 2-3 KATAS per detected question.
- Role-aware ranking: the same question may rank differently by target role.

### Evidence levels

| Level | Meaning |
| --- | --- |
| REAL_INTERVIEW | Question was asked directly in the LTM AI Strategy & Enterprise Architect interview on 2026-09-25 |
| PUBLIC_REPORTED | Question/theme was reported by another LTM candidate in public interview evidence previously researched |
| JD_DERIVED | Practice question derived from explicit responsibilities/technology requirements in the target JD |
| GENERAL_EXECUTIVE | Reusable executive/engineering interview pattern; not attributed to LTM |

## 2. Supported role packs

| Role pack | Emphasis |
| --- | --- |
| CTO | Business/technology strategy, operating model, architecture, reliability, economics, leadership |
| CAIO | AI strategy, Responsible AI, evaluation, model economics, adoption, governance |
| CDTO | Transformation roadmap, portfolio, operating model, adoption, value realization |
| ENTERPRISE_ARCHITECT | Target architecture, integration, NFRs, governance, standards, roadmaps |
| AI_PRINCIPAL_SA | Azure AI architecture, RAG, agents, observability, sizing, integration, delivery |
| AI_ML_ENGINEERING_MANAGER | Engineering execution, team leadership, MLOps/LLMOps, code, reliability |
| PLATFORM_SRE_AI | AKS, HPA/KEDA, queues, observability, SLOs, capacity, failure handling |

## 3. New KATA catalog

| ID | Topic | Evidence | Primary roles | Core method |
| --- | --- | --- | --- | --- |
| RAI-K14 | Ground-truth agent evaluation | REAL_INTERVIEW | CAIO, AI_PRINCIPAL_SA, AI_ML_ENGINEERING_MANAGER | Ground truth → retrieval → faithfulness → correctness → human validation |
| RAI-K15 | Per-question accuracy over a 1,000-question benchmark | REAL_INTERVIEW | CAIO, AI_PRINCIPAL_SA | Rubric → score each item → aggregate → segment → confidence |
| RAI-K16 | Sensitive prompt / PII handling | REAL_INTERVIEW | CAIO, EA, AI_PRINCIPAL_SA | Detect → classify → redact/block → authorize → audit |
| RAI-K17 | Token optimization / FinOps | REAL_INTERVIEW | CTO, CAIO, AI_PRINCIPAL_SA | Reduce context → route models → cache → quotas → cost/successful task |
| RAI-K18 | Ownership: personal vs team responsibility | REAL_INTERVIEW | CTO, CAIO, CDTO, AI_ML_ENGINEERING_MANAGER | My ownership → team execution → validation → production accountability |
| RAI-K19 | User / chatbot / LLM layer decomposition | REAL_INTERVIEW | EA, AI_PRINCIPAL_SA | Channel → identity → orchestration → retrieval → model → tools |
| RAI-K20 | Chatbot steps + observability | REAL_INTERVIEW | EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Trace transaction → spans → SLIs → diagnosis |
| RAI-K21 | API security and APIM policies | PUBLIC_REPORTED | EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Authenticate → authorize → rate limit → route → observe |
| RAI-K22 | Service Bus lock, retries, duplicate handling | PUBLIC_REPORTED | EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Async → Peek-Lock → idempotency → DLQ → recovery |
| RAI-K23 | HPA YAML + control-plane behavior | PUBLIC_REPORTED | AI_ML_ENGINEERING_MANAGER, PLATFORM_SRE_AI | Metric → desired replicas → controller → scheduler → node autoscaling |
| RAI-K24 | Evaluate an LLM with a biased dataset | PUBLIC_REPORTED | CAIO, AI_PRINCIPAL_SA, AI_ML_ENGINEERING_MANAGER | Population → stratify → holdout → segment metrics → uncertainty |
| RAI-K25 | Fault-tolerant AI API | PUBLIC_REPORTED | EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Timeout → retry → breaker → idempotency → async fallback |
| RAI-K26 | Python/SQL hands-on defense | PUBLIC_REPORTED | AI_PRINCIPAL_SA, AI_ML_ENGINEERING_MANAGER | Solve → explain complexity → edge cases → productionize |
| RAI-K27 | Semantic Kernel / Microsoft orchestration | JD_DERIVED | AI_PRINCIPAL_SA, CAIO | Plugin → typed function → authorization → orchestration → evaluate |
| RAI-K28 | Azure deployment from zero | JD_DERIVED | EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Landing zone → identity → AI Search/OpenAI → runtime → observability |
| RAI-K29 | Enterprise RAG on SharePoint/HR policy | REAL_INTERVIEW + JD_DERIVED | CAIO, EA, AI_PRINCIPAL_SA | Ingest → chunk → hybrid retrieval → security trim → cite → evaluate |
| RAI-K30 | P95/P99 vs SLO/SLA | GENERAL_EXECUTIVE | CTO, EA, PLATFORM_SRE_AI | Define SLI → percentile → window → error budget → contractual SLA |
| RAI-K31 | CPU/RAM sizing for Agentic AI | JD_DERIVED | CTO, EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | Measure CPU-s/task → memory/concurrency → headroom → pods/nodes |
| RAI-K32 | VRAM/GPU sizing for self-hosted LLM | JD_DERIVED | CTO, CAIO, AI_PRINCIPAL_SA | Weights → KV cache → runtime → concurrency → throughput |
| RAI-K33 | MCP sizing and tool gateway | JD_DERIVED | EA, AI_PRINCIPAL_SA | Tool RPS → CPU/RAM → auth → external limits → observability |
| RAI-K34 | Observability stack selection | JD_DERIVED | CTO, EA, AI_PRINCIPAL_SA, PLATFORM_SRE_AI | OTel → APM → Prometheus → LLM observability → load test |
| RAI-K35 | Architecture patterns for Agentic AI | JD_DERIVED | CTO, EA, AI_PRINCIPAL_SA | Pattern fit → trade-off → Azure mapping → ADR |
| RAI-K36 | Leadership of high-performing AI teams | JD_DERIVED | CTO, CAIO, CDTO, AI_ML_ENGINEERING_MANAGER | Capability map → ownership → guardrails → coaching → metrics |
| RAI-K37 | Executive architecture communication | JD_DERIVED | CTO, CAIO, CDTO, EA | BLUF → evidence → alternatives → risk/TCO → decision |
| RAI-K38 | POC → MVP → production gates | JD_DERIVED | CTO, CAIO, EA, AI_ML_ENGINEERING_MANAGER | Hypothesis → benchmark → gates → release → monitor |
| RAI-K39 | Enterprise integration: SAP/D365/ServiceNow | JD_DERIVED | EA, AI_PRINCIPAL_SA | Facade → ACL → authorization → idempotency → audit |
| RAI-K40 | First 90 days for inherited AI platform | GENERAL_EXECUTIVE | CTO, CAIO, CDTO, EA | Baseline → target model → platform → delivery gates → scorecard |

## 4. REAL_INTERVIEW KATAS — canonical prompts and answer anchors

### RAI-K14 — Ground-truth agent evaluation
**Canonical interviewer prompt:** "How do I know this agent is telling the right answer or a wrong answer?"

**Answer anchors**
1. Do not use model self-confidence as correctness.
2. Build an HR-approved golden dataset.
3. Evaluate retrieval separately from generation.
4. Measure answer correctness, faithfulness/groundedness, citation accuracy, task success.
5. Segment by policy/category/country/version.
6. Abstain/escalate when evidence is missing or conflicting.
7. Re-run regression evaluation on every material change.

**Compact answer pattern:**  
Ground truth → retrieve evidence → score answer → segment results → human review → production monitoring.

### RAI-K15 — Accuracy across 1,000 questions
**Canonical interviewer prompt:** "This thousand questions... each answer is the accuracy level. How to find it?"

**Answer anchors**
- Define an item-level rubric before testing.
- Compare each answer to approved ground truth.
- Score binary pass/fail or graded dimensions.
- Overall pass rate = passed items / eligible items.
- Report per-segment results; do not hide weak categories inside the average.
- Add inter-rater review for ambiguous items.

### RAI-K16 — Sensitive information / SSN / religion
**Canonical interviewer prompt:** "How to handle these kinds of sensitive prompts?"

**Answer anchors**
- Detect/classify sensitive data before model/tool execution.
- Apply policy: redact, block, minimize, or route to authorized HR.
- Entra ID + least privilege + document-level access control.
- Do not log raw sensitive payloads unnecessarily.
- Audit policy action, not private content.
- Treat generated/tool output with the same data controls.

### RAI-K17 — Token optimization
**Canonical interviewer prompt:** "How can we restrict burning more token, saving credits back to the customer?"

**Answer anchors**
- Hybrid retrieval + metadata filters + reranking.
- Smaller context and top-k control.
- Conversation summarization / memory policy.
- Semantic/response caching where safe.
- Model routing by task complexity.
- Max-output budgets, iteration/tool-call budgets.
- Per-consumer RPM/TPM controls.
- Optimize **cost per successful task**, not tokens in isolation.

### RAI-K18 — Ownership
**Canonical interviewer prompt:** "You or your company is handling?"

**Answer anchors**
- State personal accountability clearly.
- Separate architecture/decision/governance ownership from implementation executed by teams.
- Name what you personally validate: design reviews, NFRs, performance, production readiness.

### RAI-K19 — Layered chatbot architecture
**Canonical interviewer fragments:** "chatbot layer user layer" / "layer LLMs"

**Answer anchors**
- User/channel layer: identity, consent, UX.
- API/gateway layer: authentication, throttling, policy.
- Conversation/orchestration layer: intent, session, LangGraph/workflow.
- Knowledge layer: Azure AI Search, vector/keyword retrieval, security trimming.
- LLM layer: inference, model routing, token budgets.
- Tool/integration layer: MCP/API facade, enterprise systems.
- State/cache layer.
- Observability/security cross-cutting.

### RAI-K20 — Chatbot steps + observability
**Canonical interviewer prompt:** "Chatbot steps obs"

**Answer anchors**
User → authenticate → validate/PII → intent → retrieve → orchestrate → infer → validate → cite/respond → trace/measure.

Observability:
- OpenTelemetry: instrumentation/trace propagation.
- Application Insights/APM: requests/dependencies/errors/latency.
- Prometheus: CPU/RAM/concurrency/queue/resource metrics.
- Foundry/Langfuse: model/agent traces, tokens, quality.
- k6/Locust: controlled load generation.

## 5. PUBLIC_REPORTED KATAS

These KATAS are preparation items based on previously researched LTM interview reports. They are not guaranteed questions for a future interview.

### RAI-K21 — APIM security
Practice:
- Explain inbound/backend/outbound/on-error policy sections.
- JWT/Entra validation.
- Rate limits / quotas.
- Backend routing.
- Resource-level authorization still belongs in the application/tool boundary.

### RAI-K22 — Service Bus
Practice:
- Clarify which "timeout" is being discussed.
- Peek-Lock semantics.
- Lock renewal for long work.
- Settlement only after successful processing.
- Idempotency for side effects.
- DLQ for poison/permanent failures.

### RAI-K23 — HPA
Practice:
- Write `autoscaling/v2` YAML.
- Explain metrics APIs and HPA controller loop.
- Desired replicas calculation.
- Deployment/ReplicaSet reconciliation.
- Scheduler and Cluster Autoscaler responsibilities.
- Explain why HPA does not increase Azure OpenAI quota.

### RAI-K24 — Biased evaluation
Practice:
- Define target population.
- Stratify benchmark.
- Prevent leakage.
- Use holdout.
- Report micro + macro + worst critical segment.
- Quantify uncertainty when a segment is small.

### RAI-K25 — Fault-tolerant AI API
Practice:
- Input validation, correlation ID.
- Per-dependency timeout.
- Bounded retry + exponential backoff/jitter.
- Circuit breaker.
- Queue for long-running work.
- Idempotency for write actions.
- Failure-injection tests.

### RAI-K26 — Python/SQL
Practice:
- One SQL window-function problem.
- One Python concurrency/async problem.
- Explain edge cases, complexity, observability, production behavior.

## 6. Role-aware routing

The router should first classify:
1. question intent,
2. technical depth,
3. role,
4. evidence strength.

### Recommended weights

```text
route_score =
  0.35 * semantic_match
+ 0.25 * role_match
+ 0.15 * technology_match
+ 0.15 * seniority_match
+ 0.10 * evidence_priority
```

Evidence-priority suggestion:
- REAL_INTERVIEW = 1.00
- PUBLIC_REPORTED = 0.85
- JD_DERIVED = 0.70
- GENERAL_EXECUTIVE = 0.55

These are router design weights, not interview-scoring constants.

### Example routing

**Question:** "How do you know the HR agent is correct?"  
CAIO: RAI-K14 → RAI-K15 → RAI-K29  
AI Principal SA: RAI-K14 → RAI-K29 → RAI-K34  
Engineering Manager: RAI-K14 → RAI-K38 → RAI-K34

**Question:** "How do you reduce token consumption?"  
CAIO: RAI-K17 → RAI-K14 → RAI-K38  
CTO: RAI-K17 → RAI-K31 → RAI-K37  
AI Principal SA: RAI-K17 → RAI-K29 → RAI-K28

**Question:** "How would you scale this agent?"  
EA: RAI-K31 → RAI-K35 → RAI-K28  
Platform/SRE: RAI-K31 → RAI-K23 → RAI-K34  
CTO: RAI-K31 → RAI-K37 → RAI-K38

## 7. Role-specific practice quantity

| Role | Minimum active role KATAS | Priority |
| --- | ---: | --- |
| CTO | 14 | Strategy + architecture + reliability + economics + leadership |
| CAIO | 14 | AI strategy + evaluation + governance + economics + adoption |
| CDTO | 10 | Transformation + portfolio + operating model + adoption |
| Enterprise Architect | 16 | Architecture + integration + NFR + security + roadmaps |
| AI Principal Solutions Architect | 20 | Deep Azure AI + RAG + agents + code + sizing + integration |
| AI/ML Engineering Manager | 16 | Engineering + people + delivery + MLOps + reliability |
| Platform/SRE AI | 14 | AKS + scaling + queues + SLO + observability + capacity |

The application may recommend fewer/more based on the JD; these are curriculum targets, not hard limits.

## 8. Evaluation dimensions for the new role pack

Add to the existing executive dimensions when the selected role requires technical depth:

1. Requirement clarity.
2. Architecture decomposition.
3. Security/identity.
4. Data/retrieval correctness.
5. Agent/model evaluation.
6. Reliability/failure handling.
7. Capacity/sizing.
8. Cost/FinOps.
9. Observability.
10. Delivery/CI-CD.
11. Ownership/leadership.
12. Executive decision framing.

## 9. Short-answer mode

For live interview assistance, add a response mode:

`LIVE_GUIDE_SHORT`
- output 1-3 sentences,
- answer first,
- no preamble,
- no invented metrics,
- natural spoken English,
- prefer decision + mechanism + measurable validation.

Example:
> "I would validate the answer against an HR-approved ground-truth dataset, measuring correctness, faithfulness, and citation accuracy. I would also segment results by policy category and require the agent to abstain or escalate when evidence is missing or conflicting."
