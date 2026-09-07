# Contributing across the VAWS repos

This organization is deliberately split into independently released units. The
cost of that split is landing in the wrong repo, or landing two halves of a
change in the wrong order. This document is the routing table for both.

Read [docs/architecture.md](docs/architecture.md) first if you do not already
know which unit owns what.

## Which repo takes which change

| Change | Repo |
|---|---|
| A domain workflow: serving, benchmark, memory/torch profiling, correctness, regression, graph / distributed / operator debug, Triton lifecycle, PD serving | the scaffold |
| Parity / snapshot / materialize behavior, submodule handling, source-plane semantics | the scaffold |
| Organization development-fork work on vLLM (not a replacement community upstream) | [`vllm`](https://github.com/vllm-ascend-workspace/vllm) |
| Organization development-fork work on vLLM-Ascend (not a replacement community upstream) | [`vllm-ascend`](https://github.com/vllm-ascend-workspace/vllm-ascend) |
| Run Manifest v1 schema, experiment comparability rules | the scaffold |
| A remote tool: read / write / edit / bash / glob / grep / ls / monitor / apply_patch / job / artifact — its semantics, result shape or SSH transport | [`remote-dev`](https://github.com/vllm-ascend-workspace/remote-dev) (private / access-limited) |
| Endpoint resolution, path policy, read ledger, hook guards, the remote MCP server | [`remote-dev`](https://github.com/vllm-ascend-workspace/remote-dev) (private / access-limited) |
| Workspace / session / alias discovery for those tools | the consuming repo's resolver plugin, not `remote-dev` |
| Task identity, runtime pool, attest/publish, managed job supervision, lease renewal, coordination messaging | [`vaws-coordinator`](https://github.com/vllm-ascend-workspace/vaws-coordinator) |
| The local-first stdio `vaws_*` provider (separate from the loopback HTTP manager) | [`vaws-coordinator`](https://github.com/vllm-ascend-workspace/vaws-coordinator) — on accepted `main` `2e16e894` ([PR #2](https://github.com/vllm-ascend-workspace/vaws-coordinator/pull/2) merged) |
| Fleet collection, dashboard, history, the read-only agent query surface | [`vaws-top`](https://github.com/vllm-ascend-workspace/vaws-top) (private / access-limited) |
| Knowledge schema, redaction rules, review bot, federation or lifecycle policy, corpus entries | [`vaws-knowledge`](https://github.com/vllm-ascend-workspace/vaws-knowledge) |
| Org profile, architecture map, cross-repo routing, shared issue/PR templates | this repo (`.github`) |

If a change looks like it belongs in two repos, it is usually one contract
change plus one consumer change. Split it and read the next section.

## Dependency direction — load-bearing, not a style preference

```text
remote-dev  ←  vaws-coordinator  ←  the scaffold
```

Three rules follow, and none of them are negotiable by a single repo:

1. **`remote-dev` is a stateless substrate and must not import a consumer's
   private state.** No reading a consumer's untracked local state, no coordinator
   database, no session registry, no inventory lookup, no "if this looks like a
   VAWS worktree" special case. Everything it needs arrives as explicit endpoint
   fields — `host`, `port`, `user`, `root`, `cwd`. Workspace / session / alias
   discovery belongs in the consumer resolver plugin. If a consumer needs
   `remote-dev` to behave differently, that is a new explicit parameter, not a
   lookup.
2. **The coordinator consumes `remote-dev`, never the reverse.** The coordinator
   may call the existing tool surface and may hand back endpoint fields that work
   with it. It may not add a tool to `remote-dev`, and `remote-dev` may not learn
   about leases, bindings or executions.
3. **`vaws-top` is downstream of everything and upstream of nothing.** It reads
   host state and reports it. It must not acquire, launch, allocate or mutate.
   A monitor is never authority to kill, lease, or release devices. A PR that
   gives `vaws-top` a write path to a device, a container or a lease is wrong
   regardless of how convenient it is.

The four extracted source repositories exist. Remaining coupling is injected
configuration (inventory and host-pool file paths supplied by the consumer),
not an in-tree import. Accepted scaffold `main` consumes remote-dev, the
coordinator, and the first-stage vaws-top locator through pins and locators;
it does not ship in-tree copies of those units. Do not add new couplings from
a producer toward a consumer.

## Sequencing a change that spans repos

One PR never spans repos. Sequence it:

1. **Open a tracking issue in this repo** using the cross-repo template. It is
   the only place that holds the whole change.
2. **Land the contract in the owning repo first**, additively. A new optional
   field, a new tool, a new schema version alongside the old one — never a
   breaking edit in the same step.
3. **Land the consumer**, pinned against the shipped contract.
4. **Remove the old path last**, in the owning repo, once no consumer uses it.

Consequences of getting the order wrong are asymmetric: a consumer landed first
is broken until the producer ships, and a breaking producer change landed first
breaks every consumer at once. Additive-then-migrate-then-remove is the only
sequence that keeps every repo's `main` working on its own.

Two special cases:

- **Knowledge is one-way per layer.** A fork proposes candidates upward as a PR;
  the main repo publishes `verified` downward. Do not write `corpus/verified/`
  from a fork, and do not open a PR that reconciles the two directions.
- **Redaction is a source-side gate.** Anything that could carry an address,
  hostname, credential, token, absolute user path or internal identifier must be
  redacted in the contributing fork before the PR exists. Public Git history
  cannot be recalled, so the main repo's re-scan is a second line of defence and
  never the first.

## What must never appear in a tracked file, in any repo

IP addresses, hostnames, credentials, tokens, private keys, absolute user paths,
personal directory names, employee identifiers. This applies to prose,
examples, test fixtures, log excerpts and diagrams equally — a placeholder or a
documented example range costs nothing.

This is not hypothetical: an audit found an absolute developer path in a tracked
design document, and a remote-endpoint example carrying a real address in a
tracked README. Both were in files nobody thought of as sensitive.

## Commits and PRs

- Conventional commits: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`,
  `chore:`. Subject in the imperative, no trailing period.
- The body explains *why*, wrapped at 72–80 columns. A diff shows what changed;
  it cannot show what you rejected.
- No generated-by footers, no co-author trailers.
- Never force-push a shared branch.
- Claims need evidence. "Passing the control-plane suite" is not evidence of
  hardware execution, model readiness, operator correctness or performance —
  those are separate tiers, and a PR that needs them must attach them. State the
  environment identity you ran against.
- If you are unsure whether something is implemented or planned, check, and if
  it is still unclear, write that down. An authoritative-sounding claim nobody
  can trace is worse than an admitted gap, because it will be consumed.
