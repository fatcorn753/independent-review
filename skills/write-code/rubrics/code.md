# Rubric: code quality / コード品質

Score each criterion 0-5. Max 40 (criteria marked N/A are excluded from the max; scale the pass threshold to 80%).

| # | Criterion | What a 5 looks like |
|---|---|---|
| 1 | Readability / 可読性 | Names reveal intent; functions are small and single-purpose; comments explain why, not what |
| 2 | Maintainability / 保守性 | Easy to fix and to change the spec: low coupling, no duplication, no hidden global state, clear boundaries |
| 3 | Extensibility / 拡張性 | New features or larger scale fit without rewriting existing code; no hard-coded assumptions that block growth |
| 4 | Reliability and robustness / 信頼性・堅牢性 | Correct for the requirements; invalid, empty, huge, or unexpected input and failures (I/O, network) are handled explicitly; errors are never silently swallowed |
| 5 | Efficiency / 効率性 | Sensible algorithms and data structures; no needless CPU, memory, or I/O; no premature optimization that hurts clarity |
| 6 | Reusability / 再利用性 | Shared logic is factored into well-named units with clear interfaces, usable elsewhere without copy-paste |
| 7 | Testability / テスト容易性 | Logic is separable from I/O; dependencies can be injected; tests exist for main paths and edge cases and pass |
| 8 | Portability / 移植性 | No unneeded dependence on OS, paths, locale, or environment; configuration is externalized |

## Priorities

If the requirements state which criteria matter most (e.g. "this is a one-off script", "performance critical"), weigh those more and say so in the reasons. Do not penalize a criterion the requirements make irrelevant; mark it N/A.

## Pass condition

PASS only if total >= 80% of the max, no applicable criterion is below 3, and there is no blocking issue.

## Always blocking

- Tests, lint, type check, or build fail
- A requirement is unmet or the code gives wrong results
- Secrets, credentials, or personal data in the code
- An unhandled failure path that can lose data or crash on plausible input
- Silently swallowed errors
