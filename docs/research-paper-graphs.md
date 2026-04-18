# Research Paper Graphs

This document describes the live `research/paper` Flowhub node.

## Node Shape

```text
research/
  qianji.toml
  paper/
    qianji.toml
    paper-canonicalize.mmd
    paper-deep-read.mmd
    paper-compare.mmd
```

The `paper/` child owns three distinct scenario-case graphs:

1. `paper-canonicalize.mmd`
2. `paper-deep-read.mmd`
3. `paper-compare.mmd`

These graphs are Flowhub constraints. They are not the persistent research data
surface themselves.
Their node semantics now live in the owning
`research/paper/qianji.toml` `[[graph.node]]` contracts, so `xiuxian-qianji`
only reads and enforces the graph contract instead of hard-coding this paper
vocabulary in Rust.

## Graph Roles

### `paper-canonicalize.mmd`

Purpose:

1. intake raw paper sources
2. extract stable text, layout, and structure surfaces
3. stop only after the canonical paper package is ready

Primary process labels:

1. `pdf_intake`
2. `pdf_sniff`
3. `text_pass`
4. `layout_regions_extract`
5. `vision_patch_loop`
6. `section_link`
7. `citation_link`
8. `figure_table_index`
9. `canonicalize_done`

### `paper-deep-read.mmd`

Purpose:

1. load one canonical paper package
2. derive semantic ledgers and notebook state
3. materialize paper-level syntheses

Primary process labels:

1. `load_paper_package`
2. `methods_extract`
3. `results_extract`
4. `claim_extract`
5. `evidence_ground`
6. `limitation_extract`
7. `critique`
8. `notebook_update`
9. `materialize_syntheses`
10. `deep_read_done`

### `paper-compare.mmd`

Purpose:

1. align a topic corpus
2. compare methods, results, and contradictions
3. materialize topic-level synthesis outputs

Primary process labels:

1. `load_topic_corpus`
2. `align_schema`
3. `compare_methods`
4. `compare_results`
5. `contradiction_detect`
6. `gap_extract`
7. `review_outline`
8. `compare_done`
