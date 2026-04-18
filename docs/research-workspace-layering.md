# Research Workspace Layering

This document records the research-layered workspace model that sits around the
live Flowhub research graphs.

## Layer Split

```text
research/
  flowhub/
  runs/
  papers/
  topics/
```

Responsibilities:

1. `flowhub/` stores reusable graph contracts
2. `runs/` stores one bounded execution surface for one active run
3. `papers/` stores persistent single-paper knowledge packages
4. `topics/` stores persistent cross-paper synthesis

## Why The Split Exists

These layers must not be collapsed together:

1. the run-local execution plane is not the authority for all research state
2. persistent paper objects must survive beyond one run
3. cross-paper synthesis must have a home outside single-paper packages
4. user-visible answer previews are materialized views, not the primary
   research objects

## Localized Run Contract

The localized run surface stays intentionally small:

```text
runs/
  <run_id>/
    qianji.toml
    flowchart.mmd
    state/
    inputs/
    outputs/
    diagnostics/
```

`runs/<run_id>/outputs/response_preview.md` is a session-local answer preview.
It is not the persistent authority source for research state.

## Persistent Paper Contract

Single-paper state lives under `papers/<paper_id>/` with stable object groups
such as:

1. `source/`
2. `extraction/`
3. `structure/`
4. `semantics/`
5. `notebook/`
6. `syntheses/`

## Persistent Topic Contract

Cross-paper state lives under `topics/<topic_id>/` with stable object groups
such as:

1. `corpus/`
2. `notebook/`
3. `comparison/`
4. `syntheses/`
