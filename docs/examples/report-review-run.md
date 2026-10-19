# Example: a technical report, two review rounds

Brief: *Summarize the speed difference between `list.pop(0)` and `collections.deque.popleft()` in a minimal (~200 characters) technical report, based on your own `timeit` measurements.*
Rubric: `technical`. Python 3.9.6, macOS arm64. Measurements were made by the author agent with `timeit`.

## Round 1: REVISE (31/40)

| Criterion | Score |
|---|---|
| Purpose and audience | 3/5 |
| Correctness | 5/5 |
| Reproducibility | 4/5 |
| Quantitative evidence | 4/5 |
| Limitations | 4/5 |
| Conclusion and next steps | 4/5 |
| Structure | 4/5 |
| Writing | 4/5 |

Blocking issue:

> The brief asks for a minimal report of about 200 characters. The report body is roughly 450 characters, more than double the target.

Minor issues included: no run command, only the minimum of 5 repeats shown, false precision ("4956x" where "about 5000x" is supportable), no named audience.

## Round 2: PASS (33/40), new reviewer

The author cut the report to about half, named the audience (developers building FIFO queues), rounded the ratios and added the run command. A fresh reviewer, with no memory of round 1, found no blocking issues. Remaining minor issues: no spread across the 5 repeats, table not numbered.

## Reference check on a deliberately flawed draft

Draft excerpt:

> EMA uses α = 2/(span+1) [1]. With span=9, α is 0.2 [1]. pandas `ewm` always runs with adjust=False [1]. EMA was proposed by Brown in 1960 and is optimal for all time series [2].

`source-checker` result:

| # | Claim | Verdict | Evidence |
|---|---|---|---|
| 1 | α = 2/(span+1) | SUPPORTED | "α = 2 / (span + 1), for span ≥ 1" |
| 2 | span=9 gives α=0.2 | SUPPORTED | follows from the documented formula |
| 3a | does not ignore missing values by default | PARTIAL | `ignore_na` default is False; wording is loose |
| 3b | always runs with adjust=False | **CONTRADICTED** | `adjust` is "bool, default True" |
| 4a | proposed by Brown in 1960 | UNVERIFIABLE | source returns 404; the list says 1956 |
| 4b | optimal for all time series | UNVERIFIABLE | source unreachable; implausible as an absolute claim |

Also reported, though not planted: reference [1] says "pandas 2.2" but the URL serves the 3.0.6 documentation, and the cited URL for [2] is a placeholder domain.

## Code review on a deliberately flawed function

A 5-line `average` function with a bare `except: pass` and a failing test scored **11/40, REVISE**. The reviewer ran the test (`VERIFIED: ... FAILED`), and reported blocking issues with `file:line`: empty list returns `None` instead of 0, the bare `except` swallows `KeyboardInterrupt`, and the return type is inconsistent.
