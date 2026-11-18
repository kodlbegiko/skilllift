# SkillLift — Engineering and Research Blueprint

Status: proposed architecture, initial planning baseline. All capabilities below are targets unless separately demonstrated by a versioned evidence artifact.

## 1. Mission and thesis

Make local AI useful on ordinary hardware. Improve task-level capability by changing the execution environment, not by increasing model parameter count. Keep the infrastructure valuable as models improve: stronger models can use the same skill contracts with greater bounded autonomy.

Target users: local AI developers, users with privacy/offline requirements, researchers studying small models, and builders of assistive desktop automation. Accessibility is a use case, not a claim of accessibility compliance.

Primary hypothesis: at a fixed model, task budget and environment, structured skills plus independently verified execution can improve final-state task success relative to unstructured tool use. Secondary hypothesis: compact models with this runtime can match a larger baseline on a subset of tasks. Neither is established.

## 2. Scope and priorities

P0: deterministic fixture environment, a local text-model adapter, typed tools, a declarative skill graph, bounded decision nodes, trace, verifier, CLI, and a reproducible baseline harness.

P1: a limited macOS accessibility driver, safe desktop actions, recovery and checkpoints, richer benchmark, optional read-only browser adapter.

P2: capability-based execution strategies, MLX adapter, SDK, local trace viewer, skill authoring and compatibility reports. Cross-platform support, visual GUI fallback and registry only after measured demand.

Non-goals for v0.1: training a foundation model, arbitrary desktop/shell access, unattended payments or account changes, general-purpose agent chat, universal SKILL.md compilation, hosted inference, cloud telemetry, unrestricted plugin execution, full OS sandbox or guarantees of semantic correctness.

## 3. Architecture and responsibilities

| Component | Responsibility |
| --- | --- |
| Model adapter | Local inference, model identity, structured output, token/time limits, cancellation |
| Skill loader | Read versioned manifests and optionally discover Agent Skills metadata without executing scripts |
| Compiler | Validate declarative sources, type-check references, emit bounded graph/IR and diagnostics |
| Runtime | Explicit state machine, step budgets, cancellation, checkpointing, recovery |
| Decision engine | Present limited context/action choices and validate model proposals |
| Policy engine | Deny-by-default capabilities, scoped approvals, resource/time limits |
| Tool registry | Typed input/output, documented effects and error codes, deterministic adapters |
| Verification | Independent predicates over observed state, not model self-report |
| Evidence store | Local append-only logical event trace; redacted export and run identity |
| Benchmark | Frozen task fixtures, comparable baselines, independent scoring, reproducible reports |
| Interface | CLI first; local read-only trace viewer and SDK later |

The model is not the permission boundary. All effects must pass through a trusted dispatcher that checks the current policy and resource identity immediately before execution. Missing capability, stale approval, invalid graph, invalid output, timeout, unverified state or unavailable driver must not be treated as success.

## 4. Skill contract and intermediate representation

The initial compiler accepts an explicitly authored declarative format. Ordinary SKILL.md can provide descriptive context but is not automatically trusted, and automatic translation is an experimental future task.

Illustrative proposed YAML (not yet executable or a frozen schema):

```yaml
schema_version: 1
id: organize-pdfs
version: 0.1.0
inputs:
  workspace: path
permissions:
  filesystem:
    - root: "$workspace"
      operations: [read, move]
  network: false
limits:
  max_steps: 30
  max_model_calls: 10
  timeout_seconds: 120
steps:
  - id: inventory
    action: files.list
    args: {root: "$workspace", pattern: "*.pdf"}
  - id: classify
    decision: classify_documents
    output_schema: document_categories
    choices: [course, research, other]
  - id: plan
    action: files.propose_moves
  - id: approve
    approval: exact_move_plan
  - id: apply
    action: files.move_approved
  - id: check
    verify: inventory_count_and_hashes_preserved
```

The example omits data binding details; schema design must define them explicitly. IR nodes: action, decision, branch, approval, verify, checkpoint, bounded retry, terminal. Branches have explicit destinations; no unbounded loops or implicit arbitrary code. Graph validation checks reachability, types, capability declarations, maximum execution bounds and required terminal outcomes. Skill versions and hashes travel with every run.

## 5. Model-specific execution strategies

- Guided: deterministic workflow selects the next node; model chooses from a finite typed set.
- Structured: model supplies validated arguments and bounded branch decisions.
- Autonomous (later): model proposes short subplans assembled only from allowed tools and preconditions.

Choose a strategy by measured task/model capabilities, not parameter count alone. No model may switch its own policy, enlarge a budget, approve itself, or execute shell commands. Invalid structured output is a recorded failure or bounded retry.

Prefer semantic operations (filesystem APIs, accessibility elements, permitted DOM) over coordinate clicking. Visual screenshots and vision-based inference are a later separate research track because small text models cannot interpret raw screenshots.

## 6. Execution and evidence

Run lifecycle: CREATED → VALIDATED → RUNNING → VERIFIED_SUCCESS / FAILED / BLOCKED / CANCELLED / UNVERIFIABLE. Approval and recovery are explicit intermediate events. A verifier may reject a model-reported success. Partial completion cannot be marketed as success.

Each event records run ID, monotonically increasing sequence, skill/version/hash, tool identifier, redacted arguments/result, policy decision, error code, elapsed time and checkpoint reference. The runtime records model identity, quantization, context and sampling settings. Raw content is not logged by default. Hashes help identify artifacts; do not claim cryptographic tamper-proofing.

Checkpoints can restore runtime state and controlled fixture state. Real external actions are not universally reversible; retry only when idempotency or observed state makes it safe. Uncertain effect stops execution pending review.

## 7. Initial implementation choices

- Python 3.11+ (pin supported versions after Phase 0), typed data models, JSON Schema, pytest, Ruff and type checking.
- Local inference: Ollama on loopback only, model downloaded beforehand; test inference when network is denied. MLX optional later. No paid API, API key, or cloud fallback.
- SQLite or JSONL for local traces; choose based on measured needs.
- macOS accessibility adapter through documented APIs or a reviewed existing driver; use a dedicated test workspace and test app before real applications. Respect OS permissions.
- CLI-first; no web server needed initially. Mock tools and fixture tasks run in CI without model weights; hardware evaluation runs are separate.

Potential repository layout (planned):
```
src/skilllift/
  adapters/models/
  adapters/tools/
  compiler/
  runtime/
  policy/
  verify/
  trace/
  cli/
schemas/
skills/
benchmarks/
  tasks/
  baselines/
  evaluators/
  reports/
tests/
docs/
research/
```

## 8. Interfaces and proposed CLI

Commands below are design targets, not working commands:
```sh
skilllift doctor
skilllift validate skills/organize-pdfs
skilllift run skills/organize-pdfs --workspace ./fixture --dry-run
skilllift inspect <run-id>
skilllift bench run --suite phase0 --model local-model
skilllift bench report <run-id>
```

SDK interfaces should be small and versioned: ModelAdapter.generate, Tool.observe/execute, Policy.authorize, Verifier.evaluate, Runtime.run. Avoid freezing APIs before the smallest vertical slice is proven.

## 9. Differentiation and risk

Compare with existing computer-use runtimes, workflow engines, Agent Skills implementations, safety frameworks and benchmarks. The proposed contribution is the combination of resource-bounded small-model execution, explicit decision reduction and independently scored comparative experiments—not a claim to be first to implement any component.

Major risks: structured workflows may simply encode the answer; brittle GUI selectors; local latency and memory pressure; misleading benchmark leakage; permissions not constituting a sandbox; maintenance overhead. Mitigate through held-out variants, strong deterministic baseline, existing drivers, limited supported surfaces, honest evidence and phase gates. If a simple deterministic workflow wins, report that and narrow model involvement.

See [research protocol](docs/RESEARCH_PROTOCOL.md), [safety](docs/SAFETY.md), and [roadmap](ROADMAP.md).
