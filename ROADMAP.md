# Roadmap

Status: planned; no phase is completed. Solo-maintainer scope, approximately 16 weeks as a planning hypothesis, not a delivery promise. Work is gated by evidence, not calendar dates or file counts. Aim for a substantial medium-sized project; do not inflate code to match a size multiple.

| Phase | Indicative time | Deliverable | Gate |
| --- | --- | --- | --- |
| 0 | Weeks 1–2 | Literature/competitor review, local model feasibility, 10 isolated tasks, minimal baseline | Frozen manifest, repeatable fixtures, model identity, measured resource use, at least one end-to-end reproducible run and documented failures |
| 1 | Weeks 3–5 | Declarative schema, compiler, runtime, policy, filesystem adapter, verifier, CLI | Three skills run end-to-end; dry run; invalid graph/tool input denied; independent postconditions; deterministic mock CI |
| 2 | Weeks 6–8 | Bounded macOS computer use, checkpoint/recovery, local trace | Three supported desktop workflows in test environment; test stale UI and uncertain effects; no unauthorized effect in defined safety fixtures |
| 3 | Weeks 9–12 | Benchmark suite and comparison | 50–100 task variants if feasible, preregistered protocol, raw redacted run data, independent evaluator, error breakdown and held-out results |
| 4 | Weeks 13–16 | v0.1 release candidate, SDK subset, docs, trace viewer if core stable | Clean-install and offline run verified on reference hardware, automated checks, reproducible demo, limitations and security disclosure guidance |

## Version strategy

- v0.0.x: feasibility and contracts; unstable.
- v0.1: smallest useful local runtime and measured task suite.
- v0.2+: additional models, skills and adapters only after an observed limitation or user need.
- No public registry, broad GUI autonomy, cross-platform commitment or compatibility claims before evidence.

## Acceptance for first useful release

- No mandatory cloud inference or telemetry; local model files are pre-provisioned.
- At least three independently verified file/workspace tasks and a bounded desktop demonstration if Phase 2 gates pass.
- Skill manifests, task fixtures, outputs and run settings are versioned.
- Tests cover denial, invalid input, cancellation, timeouts, partial failures, safe retries, unverified state and absence of unexpected writes in controlled fixtures.
- Document what is simulated versus real; publish failures and negative comparisons.
- A fresh user can reproduce a small example from instructions without using personal files.

## Scope-control rules

One active milestone at a time. Prefer a vertical slice over scaffolding all modules. New features must serve a research hypothesis, fix an observed failure, or make an existing workflow usable. Do not add a dashboard, registry or additional operating system to compensate for weak core results. If Phase 0 shows no benefit over the deterministic baseline, narrow the research question before expanding.

Initial backlog: [Phase 0](docs/PHASE_0.md).
