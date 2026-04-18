# Qianji Flowhub

Qianji Flowhub is a registry of graph nodes anchored by `qianji.toml`.

The live Flowhub root is governed by [`qianji.toml`](qianji.toml). Its root
`[contract]` declares:

- which top-level graph-node directories are registered
- which required filesystem surfaces must exist under those registered nodes

`qianji check --dir "$PRJ_ROOT/qianji-flowhub"` is therefore a contract
evaluation, not a heuristic directory scan.

## Live Tree

```text
qianji-flowhub/
  qianji.toml
  coding/
    qianji.toml
  rust/
    qianji.toml
  blueprint/
    qianji.toml
  plan/
    qianji.toml
    codex-plan.mmd
  research/
    qianji.toml
    paper/
      qianji.toml
      paper-canonicalize.mmd
      paper-deep-read.mmd
      paper-compare.mmd
  wendao/
    qianji.toml
    docs-search.mmd
```

Detailed contracts stay under [`docs/`](docs/):

- [`docs/research-paper-graphs.md`](docs/research-paper-graphs.md)
- [`docs/research-workspace-layering.md`](docs/research-workspace-layering.md)

## Validation

```sh
direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  show \
  --dir "$PRJ_ROOT/qianji-flowhub"

direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  check \
  --dir "$PRJ_ROOT/qianji-flowhub"
```
