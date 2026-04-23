# scKINETICS_py311_r441

This repository is a practical fork of the original `scKINETICS` project for internal velocity benchmarking and reproducible execution in a modern software stack.

The goal of this fork is to keep the method itself close to upstream while making the codebase easier to install and run with newer Python/R environments.

## Scope of this fork

This fork focuses on:

- Python `3.10`
- R `4.4.x`
- reproducible conda-based setup for migration and batch execution
- minimal source-level compatibility fixes required to keep the workflow runnable

It is not intended as a methodological redesign of `scKINETICS`.

## Main changes

The current fork includes the following practical changes:

- updated environment files for Python `3.10`, R `4.4.1`, modern `scanpy`, `scvelo`, `rpy2`, and related dependencies
- added `mm39` support in `sckinetics/tf_targets.py`
- switched motif-calling cache behavior so downloaded genomes are kept by default instead of deleted after each run
- added runtime handling so `rpy2` follows the active conda environment R installation instead of accidentally using a different local R
- updated compatibility with newer `Biopython`, `NumPy`, and `rpy2`
- fixed motif metadata handling for `GenomeRecord` / `genes=None` workflows
- fixed `VelocityGraph.embed_graph()` for newer dependency combinations so embedding normalization works correctly during the demo workflow

## Source-level notes

Key source changes are currently concentrated in:

- `sckinetics/tf_targets.py`
- `sckinetics/graph_embedding.py`

These changes include:

- `mm39` genome and `TxDb.Mmusculus.UCSC.mm39.knownGene` support
- safer motif loading and `included` factor construction
- modern `gc_fraction(...) * 100` replacement for deprecated Biopython GC utilities
- explicit `StrVector` / `IntVector` conversion for `annotate_peaks()`
- empty-annotation safeguards in peak annotation
- conda-aware R environment setup before importing `rpy2`
- row-wise normalization in graph embedding to avoid runtime failure in later demo cells

## Environment

Validated environment:

- Python `3.10.20`
- R `4.4.1`
- `rpy2 3.6.6`
- `scanpy 1.11.5`
- `scvelo 0.3.4`

Environment files:

- `environment-sckinetics.yml`
- `sckinetics_requirements.yml`

Example:

```bash
mamba env create -f environment-sckinetics.yml
conda activate sckinetics
```

## Upstream and paper

Upstream repository:

- https://github.com/dpeerlab/scKINETICS

Original paper:

- https://academic.oup.com/bioinformatics/article/39/Supplement_1/i394/7210448
