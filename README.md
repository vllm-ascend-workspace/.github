# vllm-ascend-workspace/.github

Organization meta repository. It carries no code.

| Path | What it is |
|---|---|
| `profile/README.md` | rendered as the [organization profile](https://github.com/vllm-ascend-workspace) — routing table first |
| `docs/architecture.md` | the maintained architecture map: deployment units, the three flows, trust boundaries, authority rules, out of scope, and the dated current-state table |
| `CONTRIBUTING.md` | cross-repo contribution routing and change sequencing |
| `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md` | organization-wide defaults, used by repos that define none of their own |

This repository must stay public: GitHub only renders an organization profile
from a public `.github` repo.

Cross-repo tracking issues live here, because no single component repo can hold
a change that spans several of them.
