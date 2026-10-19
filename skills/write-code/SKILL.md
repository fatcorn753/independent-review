---
name: write-code
description: Write or change a program with an independent code-reviewer agent that evaluates each version in a fresh context (readability, maintainability, extensibility, robustness, efficiency, reusability, testability, portability), then revise until it passes. Use when the user asks to implement or refactor code and wants it independently reviewed. / プログラム作成時に、独立したレビュアーが品質基準で評価し、合格するまで修正する。
argument-hint: "[path/to/rubric.md] [files or task]"
---

# write-code

Implement the code, have an independent `code-reviewer` subagent evaluate it, revise, and repeat until it passes.
実装し、独立した `code-reviewer` に評価させ、合格するまで修正する。

## Steps

1. **Clarify inputs.** Collect the requirements, constraints, and which quality criteria matter most (e.g. one-off script vs long-lived library). Ask if unclear. Rubric: `rubrics/code.md` next to this SKILL.md (resolve to an absolute path from this skill's base directory), or a path given by the user.

2. **Implement** in the project's files, following the project's existing style. Add or update tests, and run them yourself first. Do not hand the reviewer code that you know fails.

3. **Launch the reviewer.** Call the `code-reviewer` subagent with ONLY:
   - the absolute paths of the files to review
   - the command to run tests/lint/build, if any
   - the original requirements, verbatim
   - the absolute path of the rubric file

   Do NOT pass your reasoning, design rationale, your own assessment, or what you want the verdict to be. Independence is the point.

4. **Handle the verdict.**
   - `PASS` -> go to step 6.
   - `REVISE` -> fix every blocking issue and reasonable minor issue, rerun tests, then launch a **new** reviewer call (never reuse the previous one; a fresh context is required). If you disagree with a finding, fix it or record the disagreement and reason for the final report; do not silently ignore it.
   - Keep changes within the requested scope; do not add unrelated features to please the reviewer.

5. **Stop after 3 review rounds** even if not passed. Report the remaining issues and let the user decide.

6. **Report to the user** briefly: changed files, rounds, final scores, what changed between rounds, findings you disagreed with, and what was not verified. Do not claim a pass that did not happen.

## Rules

- Never weaken or delete tests, or edit the rubric, to make the review pass.
- Never commit secrets or personal data.
- Do not commit or push unless the user asked.
