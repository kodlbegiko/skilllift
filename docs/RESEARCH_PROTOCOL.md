# Research protocol — draft, to be frozen before confirmatory runs

## Research questions

RQ1: Does structured skill execution improve final-state success for the same local model at a comparable budget?
RQ2: Which components contribute (structured decisions, verification, recovery), and which tasks do not benefit?
RQ3: On which tasks, if any, can a smaller model match a larger baseline at lower peak memory / runtime?
RQ4: What failures and policy violations occur under malformed inputs, stale state and interrupted execution?

No result exists yet. Pilot runs are exploratory and must never be reported as held-out confirmatory evidence.

## Experimental conditions

| Arm | Condition |
| --- | --- |
| A | Direct model tool loop, same typed tools and safety gate |
| B | A plus conventional human-readable SKILL.md |
| C | Structured graph, constrained decisions, same permitted tools |
| D | C plus execution-time independent verification and bounded recovery |
| E | Deterministic workflow without a model where applicable |

All arms share the same independent final evaluator and safety boundary. D uses additional online verification; final evaluation remains identical across arms. Fixed task goal, starting state, permitted effects, action/model-call limits and timeout. Report both matched-budget and natural-runtime comparisons if appropriate. Do not give C/D task-instance answers hidden from other arms. Document prompt and procedural-information equivalence and any differences that cannot be controlled.

## Models and platform

Initial candidates: same-family small local text models around 1–2B, 4B and 8B; optionally a larger reference model if memory allows. Pin exact weights/model digest, quantization, runtime version, template, context, sampling settings. Parameter count is descriptive, not a capability proxy. Reference: macOS Apple Silicon with 24 GB unified memory; record hardware, OS, thermal/power conditions and concurrent load. Record cold and warm inference separately. Do not compare different quantizations as if only parameter count differed.

## Task suite

Pilot: 10 isolated, synthetic tasks. Later target: 50–100 task variants covering files, document classification and extraction, structured transformations, bounded desktop actions, recovery and permission adversarial cases. Use licensed or generated fixtures, deterministic initial state and explicit task contracts.

Separate skill development/train-like fixtures from held-out task variants. Group by task family to prevent near-duplicate leakage. Freeze manifests, evaluator and seeds before formal experiments. Include tasks where no model is needed and tasks with a real semantic decision. Do not use evaluator internals or answer keys in prompts. Record changes as protocol deviations.

## Metrics

Primary: independently evaluated task success (binary, all required postconditions and no forbidden effects). Denominator includes timeout, invalid output and blocked runs; distinguish legitimate refusal on safety tasks using separate desired-policy-outcome metric.

Secondary: partial postconditions (not success), unsafe attempted and actual effects, policy compliance, intervention count, retry count, recovery success conditional on recoverable failures, wall time, model calls, input/output tokens where available, peak process/system memory with method, and energy only if measured reliably. Report failures by type. Do not equate local inference with zero energy cost.

Use paired tasks and repeated seeds where feasible. Report sample sizes, per-task-family results, paired differences and uncertainty (e.g. bootstrap intervals clustered by task when sufficient). Treat repeated seeds as dependent observations; do not inflate independent N. Exploratory comparisons are labeled. For claims of matching larger models define a practical equivalence margin before formal data collection, not after.

## Evidence package

Version-control task IDs, fixture hashes, baseline prompts, skill/IR hashes, environment manifest, model metadata and evaluator version. Preserve redacted run-level results and summary-generation code. Report failed and skipped runs with reasons, without silently replacing them. Keep sensitive raw logs local. A third party should be able to reproduce the synthetic suite without cloud credentials.

## Decision gates

Phase 0 is feasibility, not proof of advantage. Freeze confirmatory thresholds after pilot but before held-out results. Proceed with runtime research if it is feasible and offers a plausible gain beyond encoded deterministic workflow; if results are neutral or negative, publish the result and revise scope. Never claim broad general autonomy, safety, or model equivalence from a narrow task suite.
