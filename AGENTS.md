# AGENTS.md

Guidance for coding agents working **on** Leviathan. (To *use* Leviathan from
an agent, install [`skills/leviathan/SKILL.md`](skills/leviathan/SKILL.md).)

## Layout

| Path | What lives there |
|---|---|
| `src/config.rs` | `leviathan.toml` model, validation, CLI field flags (docs/CONFIG.md is the contract) |
| `src/fields.rs` | Field paths (`a.b`, `items[].x`) and the mapping that turns a record into what is indexed |
| `src/source.rs` | Readers: JSONL, JSON array, CSV/TSV, SQLite, stdin, gzip, directory discovery |
| `src/infer.rs` | `leviathan init`: field profiling and mapping proposal |
| `src/text.rs` | Query parsing, safe FTS5 expressions, tokens, date normalization |
| `src/index.rs` | Streaming atomic build, upsert/delete, facet counts, SQLite schema |
| `src/query.rs` | Group resolution tiers, scoped/filtered BM25 search, browse, describe, get |
| `src/card.rs` | Result-card presentation and snippets |
| `src/render.rs` | Compact text output (the default for agents) |
| `src/mcp.rs` | stdio MCP server (JSON-RPC 2.0) |
| `examples/` | Example datasets and configs; `examples/tickets` backs the integration tests |
| `bench/synth` | Deterministic synthetic maintenance log + gold queries |
| `bench/run_bench.py`, `bench/report.py` | Benchmark harness and chart rendering |

## Rules

- `cargo fmt --all`, `cargo clippy --workspace --all-targets -- -D warnings`
  and `cargo test --workspace` must pass.
- The engine is domain-neutral. Nothing in `src/` may assume a kind of data
  (tickets, logs, maintenance); domain knowledge belongs in a config.
- stdout of `leviathan mcp` is protocol only. Diagnostics go to stderr.
- The server is read-only by design. Do not add write tools, SQL passthrough
  or file access to the MCP surface.
- Never drop information silently. Truncation is marked (`…`, `shown N of M`)
  and skipped input records are counted.
- Ranking changes must come with a benchmark run (`bench/run_bench.py`) and
  the before/after numbers in the PR. Labels in `queries.jsonl` come from the
  generator, never from the ranker's output.
- Do not commit real data. Fixtures and examples are synthetic.

<!-- ci-capacity-fallback:start -->
## CI capacity and owned-runner fallback — standing owner instruction

- Use available included GitHub Actions minutes or an already authorised CI budget first. Do not increase budgets, purchase capacity, or wait for a reset when the owned-runner fallback can do the work.
- If hosted minutes, billing caps, queues, or service availability prevent execution, automatically use the repository's existing local/self-hosted runner or dedicated owned safe lane. This fallback is already authorised; do not return the CI budget decision to the owner.
- Missing runner registration, compatible runtime, workflow routing, or result storage is repository engineering/backlog work. Discover and reuse existing runners before creating anything. Record a concrete setup/repair item in the repository's existing backlog, and complete machine-remediable work within the active task's authority; do not classify it as an owner decision or substitute unrelated feature work.
- Preserve the actual verification: exact candidate identity, required tests, OS/runtime/browser coverage, security/isolation, retained logs and artifacts, and honest check results. Adapt the execution and evidence route rather than treating a historical requirement for GitHub-hosted compute as immutable. Never fabricate a green check, weaken tests, or bypass branch protection.
- A GitHub artifact-upload/storage failure is distinct from a failed build or test. Preserve evidence on an existing authorised durable local/owned store and repair the reporting path; do not repeatedly rerun unchanged tests or request spend merely to store their results.
- Escalate only a concrete action that truly requires human authority or unavailable access (for example protected runner registration, credentials, or an existing release approval), with the attempted machine recovery and smallest remaining action. Quota exhaustion by itself is not such an escalation.
- These instructions supersede older repo guidance that treats exhausted GitHub capacity as a reason to wait, seek more budget, or stop without attempting the fallback. Product, security, signing, sending, payment and deployment approval gates remain intact. A policy update does not resume a paused task.
<!-- ci-capacity-fallback:end -->
