# scKINETICS_py311_r441

This repository is a benchmark-oriented fork of the original `scKINETICS` project.

Its purpose is to keep `scKINETICS` runnable in a modern software environment used for internal RNA velocity benchmarking, while keeping the scientific core of the upstream method as close as practical to the original implementation.

This fork is intended for reproducible local / server-side execution in a conda environment. It is not a Docker-focused redistribution.

## Benchmarking context

This fork is maintained for team benchmarking and method comparison workflows.

The main goals are:

- make `scKINETICS` installable with Python `3.10` and R `4.4.x`
- make the runtime behavior more reproducible across local machines and work servers
- reduce repeated manual intervention during genome download, R package discovery, and dependency resolution
- add the minimum source-level changes needed for practical execution, especially around TF-target preprocessing and downstream demo compatibility

The intent of this fork is engineering compatibility and reproducibility, not a scientific redesign of the original method.

## Scope of this fork

This fork currently focuses on:

- Python `3.10`
- R `4.4.x`
- conda / mamba-based environments that are easier to relocate with tools such as `conda-pack`
- current `scanpy`, `scvelo`, `rpy2`, `NumPy`, and `Biopython` compatibility
- TxDb-based peak annotation workflows used by the current `scKINETICS` codebase
- initial `mm39` support with minimal intrusion into the upstream workflow

## What was changed in this fork

This fork includes both environment-level and source-level changes.

### Environment and packaging changes

- added environment specifications for Python `3.10.20` and R `4.4.1`
- aligned the validated stack around:
  - `rpy2 3.6.6`
  - `scanpy 1.11.5`
  - `scvelo 0.3.4`
- provided two conda environment files:
  - `environment-sckinetics.yml`
  - `sckinetics_requirements.yml`
- kept R / Bioconductor installation conda-oriented where possible so the environment is easier to migrate to work servers

### Source-level changes

The main source modifications are concentrated in:

- `sckinetics/tf_targets.py`
- `sckinetics/graph_embedding.py`

These changes currently include:

- added `mm39` branches for genome loading and `TxDb.Mmusculus.UCSC.mm39.knownGene`
- kept the upstream TxDb-based annotation route instead of redesigning the workflow around `EnsDb` or `BSgenome`
- changed motif-calling cache behavior so downloaded genomes are preserved by default and are deleted only when explicitly requested
- added conda-aware R initialization so `rpy2` follows the active environment R instead of accidentally binding to another local R installation
- updated `annotate_peaks()` for current `rpy2` conversion behavior using explicit `StrVector` / `IntVector`
- added empty-result protection in peak annotation to avoid downstream failures on zero-row annotation outputs
- replaced deprecated `Bio.SeqUtils.GC` usage with `gc_fraction(...) * 100`
- removed deprecated `np.str` usage in the updated compatibility path
- made motif loading more robust for current path handling and optional gene filtering
- fixed the `read_motif_file(genes=None)` and `scan_peaks()` contract so `GenomeRecord`-based motif calling does not fail on missing `included` factors
- fixed `VelocityGraph.embed_graph()` normalization so later demo steps do not fail under newer dependency combinations

## Why genome cache behavior was changed

In the upstream implementation, genome files downloaded for motif calling may be deleted after extracting the required peak sequences.

For repeated benchmarking, batch execution, or reruns on a server, that behavior leads to unnecessary re-downloads of genomes such as `mm10` and `mm39`.

This fork changes that default behavior so genome cache is retained unless the caller explicitly requests deletion. This is a runtime and reproducibility change, not a change to the scientific objective of the method.

## Current validation scope

The following pieces have been validated in the current forked environment:

- conda environment creation with Python `3.10` and R `4.4.1`
- loading Bioconductor TxDb packages from the conda environment through `rpy2`
- `PeakAnnotation(..., genome='mm39')` initialization
- `annotate_peaks()` using `TxDb.Mmusculus.UCSC.mm39.knownGene`
- `ucsc_genomes_downloader`-based access to `mm39`
- compatibility fixes needed for the later graph embedding stage exposed by the demo workflow

This should be interpreted as targeted validation of the currently used preprocessing and runtime paths.

It should not be interpreted as a blanket claim that every historical notebook cell, every possible dataset, or every downstream workflow in upstream `scKINETICS` has been fully revalidated in this fork.

## What was not intentionally changed

This fork does not intentionally redesign:

- the core EM formulation in `sckinetics/EM.py`
- the overall scientific objective of the original `scKINETICS` method
- the central modeling idea of jointly using transcriptional velocity and regulatory-network information
- the main upstream workflow structure beyond the compatibility fixes needed to keep it runnable

In short, the scientific core is intended to remain upstream-like, while the execution layer is modernized for reproducibility and benchmarking.

## Current limitations

The current `mm39` support is intentionally minimal and pragmatic.

At present, it mainly covers the TxDb-based annotation and motif-calling path used by the current codebase.

This means:

- the fork does not attempt a full species/genome abstraction refactor
- the fork does not switch the annotation strategy to `EnsDb` / `BSgenome`
- additional source changes may still be needed if future benchmarking work exercises less common branches of the original project

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

## Usage note

This repository is currently used as a local importable codebase inside the prepared conda environment.

There is no Docker workflow documented in this fork, and there is currently no separate packaging layer such as `setup.py` or `pyproject.toml` maintained here for release purposes.

## Upstream and citation

Upstream repository:

- https://github.com/dpeerlab/scKINETICS

Original paper:

- https://academic.oup.com/bioinformatics/article/39/Supplement_1/i394/7210448
