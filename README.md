# Qianji Flowhub

Qianji Flowhub is a registry of graph nodes anchored by `qianji.toml`.

The live Flowhub root is governed by its own [`qianji.toml`](qianji.toml). The
root `[contract]` declares:

- which top-level graph-node directories are registered
- which required filesystem surfaces must exist under those registered nodes

`qianji check --dir "$PRJ_ROOT/qianji-flowhub"` is therefore a contract
evaluation, not a heuristic directory scan.

## Node Contract

Every live Flowhub node is anchored by a module-root `qianji.toml`:

```text
<node>/
  qianji.toml
```

- `qianji.toml` is the public node contract.
- leaf nodes may contain only that anchor manifest.
- if a node needs owned child directories, it must declare them explicitly in
  `[contract]`.
- if a child directory is not declared by `[contract]`, `qianji check` must
  treat it as structural drift.

The live Flowhub root does not carry checked-in `template/` or `validation/`
subdirectories. Those remain test-only fixture surfaces, not part of the live
library contract.

## Active Demo Tree

The current live tree is:

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
  wendao/
    qianji.toml
    docs-search.mmd
```

The intended graph backbone is:

```mermaid
flowchart LR
  coding --> rust
  rust --> blueprint
  blueprint --> plan
```

Flowhub stores node anchors. Specific scenario relations are composed by
scenario manifests and materialized work surfaces, not by nesting unrelated
graph nodes inside one directory tree.
The top-level `wendao/` node is a sibling module that owns its own docs-search
scenario-case graph; it is not implicitly part of the planning backbone above.

Immediate `*.mmd` files under a node are scenario-case graphs owned by that
node. They must be declared by the node's `[contract].required` entries.
Scenario-case graph labels may use the fixed Flowhub control vocabulary or one
exact HTTP request label such as
`GET /api/docs/search?repo=<repo>&query=<query>&kind=<kind>&limit=<n>`.

For the live `plan` node:

```text
plan/
  qianji.toml
  codex-plan.mmd
```

`codex-plan.mmd` is a Mermaid scenario-case graph. `qianji check` parses it
through `merman-core`, resolves `merimind_graph_name` from `[[graph]].name`
or the filename-stem fallback, classifies labels matching known Flowhub
module names as graph-module nodes, and rejects malformed or uncontracted
scenario-case files. The graph must keep at least one legal module node,
avoid undeclared graph-node labels, and when multiple module nodes appear,
keep one connected module backbone between them. It does not need to cover
every registered sibling module. `qianji show --dir "$PRJ_ROOT/qianji-flowhub/plan"`
renders the owned scenario case with explicit fields for `Graph name` and
`Path`.

For the live `wendao` node:

```text
wendao/
  qianji.toml
  docs-search.mmd
```

`docs-search.mmd` is a Mermaid scenario-case graph anchored only by the local
`wendao` module while exposing the exact Wendao docs HTTP call surface the LLM
should follow for iterative search, page opening, tree inspection, navigation
expansion, and retrieval-context synthesis. Its `[[graph]]` contract also
declares `name = "DOC_SEARCH"`, so graph identity is no longer coupled to the
transport filename alone.
`qianji show --graph "$PRJ_ROOT/qianji-flowhub/wendao/docs-search.mmd"` now
renders that local flow together with the owning module contract and owning
`qianji.toml`, without injecting unrelated `blueprint/` or `plan/` surfaces.

## Scenario Composition Example

```toml
version = 1

[planning]
name = "coding-rust-blueprint-plan-demo"
tags = ["planning", "coding", "rust", "demo"]

[template]
use = [
  "coding as coding",
  "rust as rust",
  "blueprint as blueprint",
  "plan as plan",
]

[[template.link]]
from = "coding::task.coding-ready"
to = "rust::task.rust-start"

[[template.link]]
from = "rust::task.constraints-ready"
to = "blueprint::task.blueprint-start"

[[template.link]]
from = "blueprint::task.blueprint-ready"
to = "plan::task.plan-start"
```

This matches the scenario fixture at
`packages/rust/crates/xiuxian-qianji/tests/fixtures/flowhub/coding_rust_blueprint_plan/qianji.toml`
in the parent workspace.

## Validation Anchor

Flowhub layout validation is anchored in `qianji.toml`.

For the live Flowhub root:

```sh
direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  show \
  --dir "$PRJ_ROOT/qianji-flowhub"

direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  check \
  --dir "$PRJ_ROOT/qianji-flowhub"
```

For one node:

```sh
direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  check \
  --dir "$PRJ_ROOT/qianji-flowhub/rust"

direnv exec "$PRJ_ROOT" cargo run -p xiuxian-qianji --features llm --bin qianji -- \
  show \
  --dir "$PRJ_ROOT/qianji-flowhub/plan"
```

The validation model is:

1. the Flowhub root is anchored by `qianji-flowhub/qianji.toml`
2. `[contract].register` defines the allowed child graph-node directories
3. `[contract].required` defines the required filesystem surfaces for those
   nodes
4. individual nodes may declare their own `[contract]` only when they really
   own child graph-node directories or immediate local scenario-case files
5. immediate `*.mmd` scenario-case files must be declared in
   `contract.required`
6. scenario-case graphs must contain at least one legal module node but do not
   need to enumerate every registered sibling module
7. `qianji check` evaluates those contracts and reports structural drift with
   markdown diagnostics
