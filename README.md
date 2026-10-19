# independent-review

**An AI that grades its own homework is too generous.**
This Claude Code plugin has a *separate* reviewer agent, with a fresh context and read-only tools, score your report or code against a rubric, fact-check your references, and send it back until it passes.

**自分で書いたものを自分で評価すると甘くなります。**
書いた本人ではなく、まっさらなコンテキストの別エージェントが、ルーブリックで採点し、参考文献を出典に当たって検証し、合格するまで差し戻す Claude Code プラグインです。

| You ask for | What happens |
|---|---|
| a report | draft → independent review (rubric) + reference check → revise → re-review in a **new** context |
| code | implement + tests → independent review on 8 quality criteria (runs your tests) → revise |

## Install / インストール

In Claude Code:

```
/plugin marketplace add fatcorn753/independent-review
/plugin install independent-review@independent-review
```

Then run `/reload-plugins` (or restart).

## Usage / 使い方

```
/independent-review:write-report technical report.md
/independent-review:write-report academic essay.md
/independent-review:write-code
```

Or just ask in plain language: "write a report on X and have it independently reviewed."

Report arguments: `[academic|technical|path/to/rubric.md] [output file]`

## What it looks like / 実例

From a real run (full output in [docs/examples](docs/examples/)):

```
Round 1  report-reviewer  31/40  REVISE
  BLOCKING: report is ~450 chars but the brief asked for ~200
Round 2  report-reviewer  33/40  PASS   (fresh reviewer, no memory of round 1)
```

Reference check on a deliberately flawed draft:

| Claim in the report | Verdict | Evidence |
|---|---|---|
| α = 2/(span+1) | SUPPORTED | docs: "α = 2 / (span + 1), for span ≥ 1" |
| "always runs with adjust=False" | **CONTRADICTED** | docs: `adjust` is "bool, default True" |
| "proposed by Brown in 1960" [2] | UNVERIFIABLE | URL returns 404; the list itself says 1956 |

It also caught a mismatch nobody planted: the reference said "pandas 2.2" but the link serves the 3.0.6 docs.

## What gets checked / 検証内容

**Reports** (`report-reviewer`, read-only): rubric scores with a verbatim quote for every finding. Built-in rubrics: `academic` (task fit, thesis, logic, evidence, citation, analysis) and `technical` (correctness, reproducibility, quantitative evidence, limitations). Bring your own rubric as a Markdown file.

**References** (`source-checker`): runs in parallel when the report cites sources. It fetches every source itself and returns:
- **Reachability**: HTTP status and final URL (404, redirects, paywalls)
- **Bibliographic accuracy**: authors/organization, title, year, volume/pages, DOI vs the real source
- **Claim check**: `SUPPORTED / PARTIAL / CONTRADICTED / NOT FOUND / UNVERIFIABLE`, with evidence from the source
- **Cross-check**: citations with no list entry, entries never cited, implausible access dates
- **Necessity**: is the source actually used by the text?

`CONTRADICTED`, `NOT FOUND` and unreachable references are blocking. `UNVERIFIABLE` (e.g. paywalled) is never passed silently.

**Code** (`code-reviewer`): readability, maintainability, extensibility, reliability/robustness, efficiency, reusability, testability, portability ([rubric](skills/write-code/rubrics/code.md)). It may run your existing tests, linters and build (verification only). Failing tests are blocking.

## Design / 設計

- The reviewer receives only the file path, the requirements and the rubric. Never the author's reasoning or self-assessment.
- Each round uses a **new** reviewer. Maximum 3 rounds, then the remaining issues are reported to you.
- Report reviewer: `Read, Grep, Glob` only. It cannot edit anything.
- The author may not cite a source it did not read, invent a DOI/URL/date, or edit the rubric to pass.
- Web pages are treated as data, never as instructions. Only URLs go to the network, never your report text.

### Permissions and cost

`source-checker` uses `WebFetch` and `curl`, and `code-reviewer` runs your tests, so Claude Code will ask for permission. Each review round is an extra subagent run, so expect more time and tokens than a single draft.

### Limits

The reviewer is an LLM. It reduces blind spots but does not replace your own final read. It cannot verify paywalled sources unless you supply the file, and it cannot judge claims that need domain experiments it cannot run.

## Layout / 構成

```
.claude-plugin/                   plugin.json, marketplace.json
skills/write-report/              orchestration + rubrics (academic, technical)
skills/write-code/                orchestration + rubric (code)
agents/report-reviewer.md         independent report reviewer (read-only)
agents/code-reviewer.md           independent code reviewer (read-only + test runs)
agents/source-checker.md          reference fact-checker (WebFetch + curl status)
docs/examples/                    real outputs
```

## Contributing / 貢献

New rubrics (thesis, English papers, README, API design...) are the easiest contribution. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Migrating from report-review-claude

The repository and plugin were renamed. Remove the old one and install again:

```
/plugin marketplace remove report-review-claude
/plugin marketplace add fatcorn753/independent-review
/plugin install independent-review@independent-review
```

## License

MIT
