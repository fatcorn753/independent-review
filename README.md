# report-review-claude

A Claude Code plugin that writes reports with an **independent reviewer agent**.
Claude drafts the report, a separate subagent with a fresh context evaluates it against a rubric, and Claude revises until it passes.

独立したレビュアーエージェントが評価する、レポート執筆用の Claude Code プラグインです。
Claude が下書きを書き、まっさらなコンテキストの別エージェントがルーブリックで評価し、合格するまで修正します。

## Why / なぜ

An author reviewing their own draft tends to approve it. The reviewer here never sees the author's reasoning, only the file, the requirements, and the rubric, and its job is to find defects.

自分で書いたものを自分で評価すると甘くなります。レビュアーには執筆の経緯を渡さず、ファイル・要件・ルーブリックだけを渡し、欠陥を探させます。

## Install / インストール

In Claude Code:

```
/plugin marketplace add fatcorn753/report-review-claude
/plugin install report-review@report-review-claude
```

## Usage / 使い方

Ask in natural language, or call the skill directly:

```
/report-review:write-report technical report.md
```

Arguments: `[academic|technical|path/to/rubric.md] [output file]`

| Rubric | For |
|---|---|
| `academic` | University reports: task fit, thesis, logic, evidence, citation, analysis |
| `technical` | Technical reports: correctness, reproducibility, quantitative evidence, limitations |
| a file path | Your own rubric (copy `skills/write-report/rubrics/academic.md` as a starting point) |

## Code review too / コードにも使える

```
/report-review:write-code
```

`write-code` implements a program, then an independent `code-reviewer` scores it on eight software-quality criteria: readability, maintainability, extensibility, reliability/robustness, efficiency, reusability, testability, portability (`skills/write-code/rubrics/code.md`).
The code reviewer may run the project's existing tests, linters, and build with Bash (verification only; it is instructed not to modify files, install packages, or use the network). Failing tests are a blocking issue.

プログラム作成時は、可読性・保守性・拡張性・信頼性/堅牢性・効率性・再利用性・テスト容易性・移植性の8観点で評価します。レビュアーは既存のテスト・lint・ビルドの実行のみ Bash で行えます(ファイル変更・インストール・ネットワークは禁止と指示しています)。

## Flow (reports) / 流れ(レポート)

1. Claude collects the requirements and writes the draft to a file.
2. `report-reviewer` (read-only tools: Read, Grep, Glob) receives only the file path, the requirements, and the rubric.
3. It returns `PASS` or `REVISE`, per-criterion scores, and findings with verbatim quotes.
4. Claude fixes the findings and calls a **new** reviewer. Maximum 3 rounds; remaining issues are reported to you.

## Design notes / 設計メモ

- The report reviewer cannot edit files (read-only tools); neither reviewer can see the author's self-assessment.
- Every finding must include a quote from the report, so vague criticism is filtered out.
- Claude is instructed not to fabricate citations or data to please the reviewer, and not to edit the rubric to pass.
- The reviewer is an LLM: it reduces blind spots but does not replace your own final read.

## Layout / 構成

```
.claude-plugin/    plugin.json, marketplace.json
skills/write-report/SKILL.md      orchestration (draft -> review -> revise)
skills/write-report/rubrics/      academic.md, technical.md
skills/write-code/SKILL.md        orchestration for programs
skills/write-code/rubrics/        code.md (8 quality criteria)
agents/report-reviewer.md         independent report reviewer (read-only)
agents/code-reviewer.md           independent code reviewer (read-only + test runs)
```

## License

MIT
