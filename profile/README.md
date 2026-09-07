# vLLM on Ascend — a development substrate

You edit on a laptop; nothing runs there. Builds, services and measurements
happen on remote Huawei Ascend NPU hosts, inside containers, on devices someone
else also wants.

## Which repo do I need?

| I want to… | Repo | Status |
|---|---|---|
| Use the domain skills — serve, benchmark, profile, validate, debug an Ascend workload | [`vllm-ascend-workspace`](https://github.com/maoxx241/vllm-ascend-workspace) (the scaffold) | public today; **transfer into this org pending** |
| Read or contribute a verified Ascend fact — failure signature, version compatibility, capability bound | [`vaws-knowledge`](https://github.com/vllm-ascend-workspace/vaws-knowledge) | contracts shipped; corpus empty; tooling not written |
| Give an agent remote read/edit/bash/search/patch/job/artifact over SSH | `remote-dev` | **extraction in progress** — code lives in the scaffold at `.remote-dev/` |
| Look at what the NPU fleet is actually doing right now | `vaws-top` | **extraction in progress** — code lives on the scaffold's `vaws-top` branch |
| Get an exclusive NPU lease, a prepared runtime, or a supervised long job | `vaws-coordinator` | **extraction in progress** — code lives in the scaffold at `.agents/coordinator/` |

Unlinked names are not yet published. Do not guess their URLs; follow the
tracking issues in this repo.

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

`remote-dev` must never reach back into a consumer's private state. The
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
