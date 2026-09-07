# vLLM on Ascend — a development substrate

You edit on a laptop; nothing runs there. Builds, services and measurements
happen on remote Huawei Ascend NPU hosts, inside containers, on devices someone
else also wants.

## Which repo do I need?

Status checked **2026-09-07** against each repository's `main` and the cited
PRs. A repository existing is not code merged; an open PR or a green check is
not merged, deployed, or hardware-tested.

| I want to… | Repo | Status (2026-09-07) |
|---|---|---|
| Use the domain skills — serve, benchmark, profile, validate, debug an Ascend workload | [`vllm-ascend-workspace`](https://github.com/maoxx241/vllm-ascend-workspace) (the scaffold) | public; **org transfer pending**; consumer PRs [#84](https://github.com/maoxx241/vllm-ascend-workspace/pull/84), [#90](https://github.com/maoxx241/vllm-ascend-workspace/pull/90), [#91](https://github.com/maoxx241/vllm-ascend-workspace/pull/91), [#92](https://github.com/maoxx241/vllm-ascend-workspace/pull/92), [#95](https://github.com/maoxx241/vllm-ascend-workspace/pull/95) are **open** |
| Read or contribute a verified Ascend fact — failure signature, version compatibility, capability bound | [`vaws-knowledge`](https://github.com/vllm-ascend-workspace/vaws-knowledge) | public; `tools/` `server/` `sync/` `conformance/` on main ([#1](https://github.com/vllm-ascend-workspace/vaws-knowledge/pull/1)–[#4](https://github.com/vllm-ascend-workspace/vaws-knowledge/pull/4) merged); review bot [#5](https://github.com/vllm-ascend-workspace/vaws-knowledge/pull/5) **open**; corpus empty |
| Give an agent remote read/edit/bash/search/patch/job/artifact over SSH | [`remote-dev`](https://github.com/vllm-ascend-workspace/remote-dev) | **exists** (private / access-limited); tool surface on its `main`; scaffold consumer [#90](https://github.com/maoxx241/vllm-ascend-workspace/pull/90) **open**; live SSH re-validation of the standalone tree **not claimed** |
| Look at what the NPU fleet is actually doing right now | [`vaws-top`](https://github.com/vllm-ascend-workspace/vaws-top) | **exists** (private / access-limited); collection / dashboard / read-only MCP on its `main`; observation only — never an allocation authority |
| Get an exclusive NPU lease, a prepared runtime, or a supervised long job | [`vaws-coordinator`](https://github.com/vllm-ascend-workspace/vaws-coordinator) | public; loopback HTTP manager on `main`; stdio `vaws_*` provider [#2](https://github.com/vllm-ascend-workspace/vaws-coordinator/pull/2) **open**; hardware-level guarantees **not established** |

Private links are the real repositories. Lack of public access is not
nonexistence, and is not successful public verification. Commit SHAs and the
merged / pending split: [architecture map](https://github.com/vllm-ascend-workspace/.github/blob/main/docs/architecture.md#what-is-actually-built).

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
  — deployment units, the three flows, trust boundaries, out of scope.
- [Cross-repo contribution routing](https://github.com/vllm-ascend-workspace/.github/blob/main/CONTRIBUTING.md)
  — which repo takes which change, and how to sequence one that spans repos.
