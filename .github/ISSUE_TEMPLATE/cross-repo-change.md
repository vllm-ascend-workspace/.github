---
name: Cross-repo change
about: A change that needs PRs in more than one repo. File this in the .github repo.
title: 'cross-repo: '
labels: cross-repo
---

**What has to change, end to end**

<!-- One paragraph. The whole change, not one repo's slice of it. -->

**Contract owner** (the repo that defines the schema, tool or API being changed)

**Sequence** — additive contract first, consumer second, old path removed last:

- [ ] `owner-repo` — additive contract change · PR:
- [ ] `consumer-repo` — consume it · PR:
- [ ] `owner-repo` — remove the old path · PR:

**Direction check**

- [ ] `remote-dev` gains no knowledge of a consumer's private state
- [ ] no new dependency pointing from a producer toward a consumer
- [ ] `vaws-top` gains no write path to a device, container or lease
