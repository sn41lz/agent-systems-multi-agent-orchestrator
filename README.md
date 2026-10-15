# 04 · Multi-Agent Orchestrator

Explicit supervisor-worker DAG execution with scoped handoffs, bounded fan-out, and evaluator-optimizer verification.

**Status: planning — implementation has not started.** This README describes intended scope and acceptance criteria. It does not establish working behavior, completed tests, security guarantees, or measured performance.

Week 4 of an eight-repository agent-systems portfolio.

## Planned scope

- Typed task inputs and outputs with dependency-aware scheduling and bounded concurrency.
- Scoped context handoffs that preserve requirements, evidence, and artifact references.
- A bounded evaluator-optimizer cycle with explicit verification outcomes.

## Planned setup and observability

The implementation is intended to provide a `docker-compose.yml` and `Makefile` so a reviewer can use `make demo` for a deterministic offline demonstration and `make test` for offline regression checks. **These commands and their supporting files are not implemented yet.** Live-model runs will be optional and separately documented with configuration and cost assumptions.

Every model call and tool execution is intended to emit an OpenTelemetry span. Planned aggregate reporting includes token usage, estimated USD per 1,000 requests, p50/p95 latency, and p50/p95 time to first token (TTFT) where streaming applies. Offline fixtures can verify instrumentation and behavior; live-provider performance requires separate measurements.

The core mechanics will use raw model SDKs with explicit control flow; no LangChain, LlamaIndex, or other orchestration framework will implement them.

See [PRODUCTION_CHECKLIST.md](PRODUCTION_CHECKLIST.md) for shared acceptance criteria.

## Proposed architecture and tradeoffs

A supervisor constructs a validated task DAG. A scheduler dispatches ready nodes to raw-SDK workers with scoped context, and reducers collect typed outputs. An evaluator can request a bounded revision or produce a terminal acceptance or failure result.

Parallel workers can reduce critical-path latency while increasing total tokens and coordination overhead. Narrow handoffs reduce context size but can omit necessary constraints. Evaluator agreement does not by itself establish correctness.

## Roadmap and acceptance criteria

Complete the shared reproducibility and observability checklist, then implement and verify these project-specific criteria:

- [ ] Reject cyclic or invalid dependency graphs and schedule independent nodes with bounded fan-out.
- [ ] Expose each task handoff and its context scope in traces, including cancellation and upstream failures.
- [ ] Enforce iteration and budget caps on evaluator-optimizer cycles.
- [ ] Compare a single-agent baseline with DAG execution on the same tasks, scoring correctness, total cost, and critical-path latency.

## Candidate failure modes

These are design risks to investigate, **not tested or observed findings**:

- A handoff loses a requirement or worker assumptions conflict at the merge step.
- An upstream failure leaves downstream tasks waiting indefinitely.
- An evaluator rubber-stamps a plausible but incorrect answer, or revision cycles consume the budget.

The implementation will add reproduction cases, actual outcomes, and mitigations as evidence becomes available.

## Baseline versus optimized metrics

No benchmark has been run. The following comparison is planned:

- **Baseline:** One agent handles each complete task with the same tools and evaluation rubric.
- **Optimized:** Scoped DAG workers with bounded evaluator-optimizer verification.

| Metric | Baseline | Optimized |
| --- | --- | --- |
| Input/output/total tokens per request | Not yet measured | Not yet measured |
| Estimated USD per 1,000 requests | Not yet measured | Not yet measured |
| End-to-end latency p50 | Not yet measured | Not yet measured |
| End-to-end latency p95 | Not yet measured | Not yet measured |
| Streaming TTFT p50, where applicable | Not yet measured | Not yet measured |
| Streaming TTFT p95, where applicable | Not yet measured | Not yet measured |
| Project-specific quality and failure rate | Not yet measured | Not yet measured |

Future results must identify the workload revision, model/provider configuration, environment, concurrency, sample counts, and quality criteria. Cost estimates must disclose pricing date and assumptions. Report the complete workload, including errors and retries, and retain raw results so baseline and optimized runs can be reproduced. Do not substitute target values for measurements.

## Portfolio

- [01 · Raw Agent Tool Loop](https://github.com/sn41lz/agent-systems-01-tool-loop)
- [02 · Context Window Engine](https://github.com/sn41lz/agent-systems-02-context-window)
- [03 · Hierarchical Agent Memory](https://github.com/sn41lz/agent-systems-03-hierarchical-memory)
- [04 · Multi-Agent Orchestrator](https://github.com/sn41lz/agent-systems-04-multi-agent-orchestrator)
- [05 · Agent Evaluation Harness](https://github.com/sn41lz/agent-systems-05-eval-harness)
- [06 · Secure Code Sandbox](https://github.com/sn41lz/agent-systems-06-secure-sandbox)
- [07 · Durable Agent Runtime](https://github.com/sn41lz/agent-systems-07-durable-runtime)
- [08 · Agent Platform API and SDKs](https://github.com/sn41lz/agent-systems-08-api-sdk)
