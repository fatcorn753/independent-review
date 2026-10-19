---
name: write-report
description: Write or improve a report (university or technical) with an independent reviewer agent that evaluates each draft in a fresh context, then revise until it passes. Use when the user asks to write, draft, or polish a report. / レポートを書く・推敲するときに、独立したレビュアーに評価させながら仕上げる。
argument-hint: "[academic|technical|path/to/rubric.md] [output file]"
---

# write-report

Write a report, have it evaluated by an independent `report-reviewer` subagent, revise, and repeat until it passes.
レポートを書き、独立した `report-reviewer` に評価させ、合格するまで修正する。

## Steps

1. **Clarify inputs.** Collect the requirements (assignment text, brief, audience, length, language) and the source material. If they are missing, ask the user. Pick the rubric:
   - `academic` -> `rubrics/academic.md` (university reports)
   - `technical` -> `rubrics/technical.md` (technical reports)
   - a path given by the user -> use that file
   - not specified -> infer from the requirements and say which one you chose

   Rubric files are in the `rubrics/` directory next to this SKILL.md. Resolve them to an absolute path from this skill's base directory.

2. **Write the draft to a file** (default: `report.md` in the working directory, or the file the user named). Do not only print it in chat; the reviewer reads the file.

3. **Launch the reviewer.** Call the `report-reviewer` subagent with ONLY:
   - the absolute path of the report file
   - the original requirements, verbatim
   - the absolute path of the rubric file

   Do NOT pass your reasoning, your own assessment ("this is strong"), the drafting history, or what you want the verdict to be. Independence is the point; leaking your view biases the review.

4. **Handle the verdict.**
   - `PASS` -> go to step 6.
   - `REVISE` -> fix every blocking issue and reasonable minor issue by editing the file. If you disagree with a finding, do not silently ignore it: fix it, or record the disagreement and its reason for the final report.
   - Then launch a **new** reviewer call (never reuse the previous one with SendMessage; a fresh context is required) and repeat.

5. **Stop after 3 review rounds** even if not passed. Do not loop forever. Report the remaining issues to the user and let them decide.

6. **Report to the user** briefly: the file path, the number of rounds, the final scores, what changed between rounds, any findings you disagreed with, and anything the reviewer could not verify. Do not claim a pass that did not happen.

## Rules

- Never fabricate citations, data, or experimental results to satisfy the reviewer. If the reviewer flags missing evidence you do not have, tell the user what is needed.
- Never edit the rubric to make a draft pass.
- Write the report in the language of the requirements unless the user says otherwise.
