# Wendao Docs Capability Contracts

This document records the Flowhub-side contract boundary for the live Wendao
docs lane.

## Ownership Split

The lane is intentionally split across three owners:

1. `qianji-flowhub/wendao/qianji.toml` owns the graph contract
2. `xiuxian-qianji` owns parsing, validation, and rendering logic
3. `xiuxian-wendao` owns the executable capability assets and transport
   adapters

That means the visible graph vocabulary must come from Flowhub contract files,
not from a Rust-owned fixed node taxonomy.

## Stable Contract IDs

The current docs lane uses stable contract IDs as primary graph nodes:

1. `wendao.docs.search`
2. `wendao.docs.document`
3. `wendao.docs.document_structure`
4. `wendao.docs.navigation`
5. `wendao.docs.retrieval_context`

Their `kind`, `role`, and `agent_action` fields are declared under
`qianji-flowhub/wendao/qianji.toml` `[[graph.node]]`.

## Transport Boundary

These contract IDs are transport-neutral business handles.

Current boundary rules:

1. the Flowhub graph names the business capability, not the HTTP route
2. HTTP `/api/docs/*` surfaces are compatibility adapters, not the source of
   truth for the graph contract
3. Arrow Flight remains the preferred direction for a purer business transport
   boundary, but the Flowhub graph does not need to encode transport syntax in
   order to express that direction

## What Rust Still Owns

`xiuxian-qianji` still owns generic logic:

1. load `qianji.toml`
2. parse Mermaid
3. validate that every non-module graph node is declared
4. render the declared contract surface on `qianji show --graph`

When a node has no matching `[[graph.node]]` contract, the renderer should stay
generic and defensive instead of inventing a business meaning.
