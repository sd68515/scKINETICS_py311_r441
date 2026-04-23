# scKINETICS_py311_r441

This repository is a compatibility-oriented fork of the original `scKINETICS` project.

The purpose of this fork is to support installation, execution, and reproducibility in a modern software environment, especially for:

- Python `3.10`
- R `4.4.x`
- newer `rpy2`, `Biopython`, `NumPy`, `scanpy`, and `scvelo`
- reproducible environment setup for team benchmarking work

## Benchmarking context

This fork is maintained to support internal velocity benchmarking and reproducible execution in a modern software stack.

This statement is provided for provenance and reproducibility only.

It should not be interpreted as a redesign of the original `scKINETICS` scientific method.

## What was changed in this fork

This fork includes both environment-level updates and a small set of source-level compatibility / usability fixes needed to make the project runnable in the current benchmarking setup.

The main categories of changes are:

- updated environment specifications for Python `3.10`, R `4.4.1`, modern `rpy2`, `scanpy`, and related dependencies
- added reproducible conda environment files:
  - `environment-sckinetics.yml`
  - `sckinetics_requirements.yml`
- adjusted package/runtime compatibility for newer library versions
- added initial `mm39` support in the TF-target preprocessing / annotation layer
- changed genome cache handling so repeated runs do not re-download genomes by default

## Source-level changes already included

The current public fork already contains source modifications in `sckinetics/tf_targets.py`, including:

- added `mm39` branches for genome loading and `TxDb.Mmusculus.UCSC.mm39.knownGene`
- changed `call_motifs()` so genome cache is preserved by default and only deleted when explicitly requested via `delete_genome_cache=True`
- replaced deprecated `Bio.SeqUtils.GC` usage with modern `gc_fraction(...) * 100`
- replaced deprecated `np.str` usage with built-in `str`
- updated `rpy2` data conversion so `annotate_peaks()` works with current `rpy2` versions
- made `read_motif_file()` more robust for current path handling and optional gene filtering

These changes are intended to address runtime compatibility, repeated-download friction, and genome support in preprocessing.

## What was not changed

This fork does **not** intentionally modify:

- the core EM formulation in `sckinetics/EM.py`
- the main scientific objective of the original method
- the central modeling idea of learning transcriptional velocity together with a regulatory network
- the benchmark-facing interpretation of `scKINETICS` as a method

In other words, this fork is intended to make `scKINETICS` runnable, reproducible, and benchmark-friendly in a modern environment, while keeping the scientific core as close as practical to the upstream implementation.

## Note on genome cache behavior

The upstream implementation deletes downloaded genome cache during motif calling after extracting the required peak sequences.

For repeated execution and batch benchmarking, this causes unnecessary re-downloads of genomes such as `mm10` / `mm39`.

This fork changes that behavior so genome cache is kept by default. This is a runtime / usability change and is not intended to alter the scientific method itself.

## Environment

The main tested environment in this fork is:

- Python `3.10.20`
- R `4.4.1`
- `rpy2 3.6.6`
- `scanpy 1.11.5`
- `scvelo 0.3.4`

Two environment files are provided:

- `environment-sckinetics.yml`: smaller, benchmark-oriented reproducible environment
- `sckinetics_requirements.yml`: broader environment file aligned with the currently validated local setup

Example:

```bash
mamba env create -f environment-sckinetics.yml
conda activate sckinetics
```

## Upstream project

The original upstream repository is:

- https://github.com/dpeerlab/scKINETICS

This fork exists to support practical installation, validation, and benchmarking in newer software stacks.

## Paper

Original paper:

- https://academic.oup.com/bioinformatics/article/39/Supplement_1/i394/7210448
