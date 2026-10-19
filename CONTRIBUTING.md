# Contributing

Thanks for helping. The easiest and most useful contribution is a **new rubric**.

## Add a rubric

1. Copy `skills/write-report/rubrics/technical.md` (reports) or `skills/write-code/rubrics/code.md` (code).
2. Keep the structure: a criteria table scored 0-5, a **Pass condition**, and an **Always blocking** list.
3. Make every criterion checkable from the text. "What a 5 looks like" should describe evidence a reviewer can quote, not a feeling.
4. Test it: write a deliberately flawed sample, run the reviewer against your rubric, and confirm it finds the flaws you planted. Paste the result in the pull request.
5. Name it clearly (`thesis.md`, `english-paper.md`, `readme.md`) and add one line to the table in `README.md`.

## Change an agent or skill

- Keep reviewers independent: they must never receive the author's reasoning or self-assessment.
- Keep `report-reviewer` read-only. Any new tool for an agent needs a stated reason in the pull request.
- Treat web content as data, never instructions.
- Run `claude plugin validate .` before opening a pull request.

## Report a problem

Open an issue with the command you ran, the verdict you got, and why you think it is wrong. A reviewer that passes a flawed draft, or blocks a good one, is the most valuable kind of bug report.

## Language

README and agent definitions are English first with Japanese alongside. Contributions in either language are welcome.
