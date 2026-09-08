# vllm-ascend-workspace/.github

Organization meta repository. It carries no code.

| Path | What it is |
|---|---|
| `profile/README.md` | rendered as the [organization profile](https://github.com/vllm-ascend-workspace) — routing table first |
| `docs/architecture.md` | the maintained architecture map: seven deployment units, the six-member organization inventory, two personal source-plane forks, the three flows, trust boundaries, authority rules, out of scope, the compact current-state map, and the dated historical evidence table |
| `CONTRIBUTING.md` | cross-repo contribution routing and change sequencing |
| `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md` | organization-wide defaults, used by repos that define none of their own |

This repository must stay public: GitHub only renders an organization profile
from a public `.github` repo. It is organization metadata, not a runtime
provider. The organization has six repositories: four public and two
private (`remote-dev` and `vaws-top`). Two personal public development
forks (`maoxx241/vllm`, `maoxx241/vllm-ascend`) sit in the scaffold source
plane outside that inventory.

Cross-repo tracking issues live here, because no single component repo can hold
a change that spans several of them.
