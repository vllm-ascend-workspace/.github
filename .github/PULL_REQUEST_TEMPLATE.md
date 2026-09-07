<!-- Why, not what. The diff already shows what. -->

**Why**

**Evidence** — what you actually ran, and against which environment. Control-plane
tests do not establish hardware execution, model readiness, operator correctness
or performance; attach those separately if the change claims them.

**Checks**

- [ ] no addresses, hostnames, credentials, tokens or absolute user paths in
      tracked files
- [ ] dependency direction unchanged (see CONTRIBUTING.md), or this is the
      owning repo's own contract
- [ ] cross-repo work has a tracking issue in `vllm-ascend-workspace/.github`
- [ ] anything described as implemented is implemented; gaps are written down
