# vLLM on Ascend — a development substrate

You edit on a laptop; nothing runs there. Builds, services and measurements
happen on remote Huawei Ascend NPU hosts, inside containers, on devices someone
else also wants.

## Which repo do I need?

Status checked **2026-09-08** against Root's authenticated All six-row
organization inventory (~01:30 UTC), fresh public metadata and HEAD/main
refs (01:33:33–01:33:40 UTC), and the independently accepted personal-fork
restoration. A repository existing is not code merged; an open PR or a
green check is not merged, deployed, or hardware-tested. The organization
has six repositories: four public, two private (`remote-dev` and
`vaws-top`). Private numeric metadata remains explicitly dated.

The organization has six repositories and seven deployment units.
Repository count is not runtime-unit count. Two personal public business
forks sit in the scaffold source plane outside that inventory; they are
not extra services. `.github` is metadata, not a provider.

| I want to… | Repo | Status (2026-09-08) |
|---|---|---|
| Use the domain skills — serve, benchmark, profile, validate, debug an Ascend workload | [`vllm-ascend-workspace`](https://github.com/vllm-ascend-workspace/vllm-ascend-workspace) (the scaffold, id 1196723340) | public, non-fork; same-id transfer complete; accepted `main` `7af4ac31`; consumes remote-dev, coordinator, and the first-stage vaws-top locator; boundary baseline accepted rows: **zero**; not installed-runtime or hardware evidence |
| Read or contribute a verified Ascend fact — failure signature, version compatibility, capability bound | [`vaws-knowledge`](https://github.com/vllm-ascend-workspace/vaws-knowledge) | public; accepted `main` `1eac65cf` (Stage 2 [#9](https://github.com/vllm-ascend-workspace/vaws-knowledge/pull/9) merged); tools, engine, sync, conformance kit and deterministic review bot on `main`; central collection/proposal/advisory/snapshot **source** available; corpus still empty placeholders; preview [34144173739](https://github.com/vllm-ascend-workspace/vaws-knowledge/actions/runs/34144173739) PASSed with 0 eligible input; proposal/snapshot semantic outcomes are the bounded empty/no-op results in the current source map, not candidate creation or verified qualification |
| Give an agent remote read/edit/bash/search/patch/job/artifact over SSH | [`remote-dev`](https://github.com/vllm-ascend-workspace/remote-dev) | **exists** (private / access-limited); accepted `main` `b6acc21d`; scaffold consumer on accepted `main`; issues [#1](https://github.com/vllm-ascend-workspace/remote-dev/issues/1) and [#2](https://github.com/vllm-ascend-workspace/remote-dev/issues/2) remain **open**; glob/Python 3.9 and the narrow mux interruption path are in current source; mux isolates the deliberate interruption path only; live SSH re-validation of the standalone tree **not claimed** |
| Look at what the NPU fleet is actually doing right now | [`vaws-top`](https://github.com/vllm-ascend-workspace/vaws-top) | **exists** (private / access-limited); `main` `e1347848`; observation only — never an allocation authority; first-stage scaffold locator present; not a deployment claim |
| Get an exclusive NPU lease, a prepared runtime, or a supervised long job | [`vaws-coordinator`](https://github.com/vllm-ascend-workspace/vaws-coordinator) | public; accepted `main` `2e16e894` has the loopback HTTP manager **and** the stdio `vaws_*` provider; hardware-level guarantees **not established** |
| Read the org profile, architecture map, or open a cross-repo tracking issue | [`.github`](https://github.com/vllm-ascend-workspace/.github) (id 1360014025) | public metadata; not a provider; public `main` `fc6a1929` |

Personal business forks are source-plane repositories **outside** the
organization inventory. Same numeric ids; independently verified same-id
return to `maoxx241`. They are not organization members and not new
services.

| I want to… | Repo | Status (2026-09-08) |
|---|---|---|
| Work on the personal vLLM development fork | [`maoxx241/vllm`](https://github.com/maoxx241/vllm) (id 1009465986) | public fork of [`vllm-project/vllm`](https://github.com/vllm-project/vllm) (id 599547518); `main` `a435e310`; **not** a replacement community upstream; source-plane under the scaffold flow |
| Work on the personal vLLM-Ascend development fork | [`maoxx241/vllm-ascend`](https://github.com/maoxx241/vllm-ascend) (id 924147541) | public fork of [`vllm-project/vllm-ascend`](https://github.com/vllm-project/vllm-ascend) (id 924058625); `main` `d52c1b8d`; **not** a replacement community upstream; source-plane under the scaffold flow |

Private links are the real repositories. Lack of public access is not
nonexistence, and is not successful public verification. Current SHAs,
consumer wiring and operational limits: [current source map](https://github.com/vllm-ascend-workspace/.github/blob/main/docs/source-map.md#current-source-map).
The dated 2026-09-07 evidence table is retained as [historical snapshot](https://github.com/vllm-ascend-workspace/.github/blob/main/docs/source-map.md#what-is-actually-built).

## How they relate

Three flows hold the org together: getting local edits onto a remote runtime,
acquiring NPU devices exclusively, and turning runs into reusable evidence. The
dependency direction is the point of the split, and it only points one way:

```text
the scaffold            domain skills + source plane; consumes all four below
  ├─ remote-dev         stateless SSH tool surface; depends on nothing else here
  ├─ vaws-coordinator   task identity, runtime pool, job supervision;
  │                     consumes remote-dev, never the reverse
  ├─ vaws-top           observation only; never an allocation decision
  └─ vaws-knowledge     one-way: forks propose upward, verified flows downward
```

`remote-dev` must never reach back into a consumer's private state. Workspace,
session and alias discovery belong in the consumer's resolver plugin. The
coordinator consumes `remote-dev`, never the reverse. See
[CONTRIBUTING.md](https://github.com/vllm-ascend-workspace/.github/blob/main/CONTRIBUTING.md).

## Three rules that are easy to lose in a multi-repo split

- The **host NPU queue is the sole device-allocation authority.** It lives on
  each Ascend host. Nothing else allocates a device.
- **`vaws-top` is observation-only.** Its numbers are for humans and for
  narrowing a search. Never decide an allocation from them.
- **An MCP fence is cooperative, not an OS boundary.** Direct SSH, unmanaged
  containers and plain remote-dev writes are outside it by construction.

## Read next

- [Architecture map](https://github.com/vllm-ascend-workspace/.github/blob/main/docs/architecture.md)
  — seven deployment units, six-member organization inventory, two personal
  source-plane forks, the three flows, trust boundaries, out of scope.
- [Cross-repo contribution routing](https://github.com/vllm-ascend-workspace/.github/blob/main/CONTRIBUTING.md)
  — which repo takes which change, and how to sequence one that spans repos.
