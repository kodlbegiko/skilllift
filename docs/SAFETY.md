# Safety and trust boundaries

Status: proposed requirements, not implemented or audited. SkillLift is not a security sandbox or a guarantee of safe computer use.

## Threat model

Untrusted inputs include model outputs, skill files from third parties, document text, web content, application output, screenshots, and tool results. They may contain instructions or attempts to trigger unauthorized actions. The runtime, reviewed tool adapters, host OS and user-controlled policy are trusted components; OS or dependency compromise is out of scope.

## Initial boundaries

- Run on synthetic fixtures in a dedicated workspace; default deny access outside it.
- No remote inference, external network, arbitrary shell, credential store access, or implicit installation of third-party code in the first release.
- Enforce canonical resource paths and reject symlink/path escapes; address race conditions and revalidate effect targets immediately before action. Prefer OS-enforced isolation where available. Application-level checks alone are not a sandbox.
- Model output is a typed proposal. The dispatcher authorizes each individual effect against a user-defined policy. Never trust a model's statement that an action is safe or approved.
- Reads are also scoped. Do not expose clipboard, private screen, home directory or other applications by default.
- macOS Accessibility permissions may grant broad OS access. Restrict the driver to explicit test apps/windows and require user awareness; do not claim that restriction provides OS-level isolation.
- Destructive operations, irreversible external effects, sending messages, payments, account/security changes, credential use or external uploads are excluded from v0.1. File moves within fixtures require exact-plan approval when enabled; file deletion is not supported.
- Approval is bound to exact effects, destination, scope, run and freshness; a changed plan invalidates approval. No self-approval.
- Provide dry-run, cancellation, step/time/model-call budgets, and fail-closed behavior. A dry run must never call effectful tools.
- Retry only idempotent or verified-safe effects. If a tool reports ambiguous completion, observe state before deciding; stop if uncertain.
- Keep logs local and minimize contents. Redact sensitive fields, make exports opt-in and do not capture raw screenshots by default. A trace is not proof of security.

## Safety tests

Fixtures must include path traversal, symlink escapes, malformed tool args, unknown tool IDs, permission escalation attempts, untrusted document instructions, stale approval, stale UI targets, timeout, cancellation and ambiguous partial effects. Test both denied and legitimate authorized actions. Assert final state and absence of unintended effects, not just a refusal message.

## Reporting

Open a public issue for non-sensitive bugs. For vulnerabilities, prefer GitHub private vulnerability reporting if enabled; otherwise request a private channel without publishing exploit details or private data. Do not claim private reporting is enabled until verified.
