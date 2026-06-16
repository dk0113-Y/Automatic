# Automatic

Summer internship portfolio repository for a GPT / Codex / AI-assisted
engineering workflow.

`Automatic` is a lightweight control-plane repository. It packages prompt
interfaces, Codex skill contracts, structured training-analysis digests, and
history records for a reinforcement-learning training project.

The name `Automatic_2` appears in some older tracked documents and historical
payloads. In the current GitHub repository, the public repository name is
`Automatic`; `Automatic_2` should be read as an earlier internal workspace
name preserved for history compatibility.

## Project Overview

This repository shows how I organized an AI-assisted research and development
workflow around a training project:

- GPT is used for tuning review, evidence interpretation, and next-step
  planning.
- Codex is used for bounded file operations, factual extraction, archive
  landing, and validation against explicit contracts.
- Tracked JSON and Markdown records keep the discussion grounded in compact
  evidence instead of raw logs, checkpoints, or local run folders.

The repository is designed to be readable in one or two minutes by a reviewer
who wants to understand the workflow, not rerun the training system.

## Motivation

Training experiments produce many artifacts: logs, CSV files, model outputs,
plots, checkpoints, and tuning discussions. If those artifacts are copied
directly into a documentation repository, the result becomes difficult to
review and risky to publish.

`Automatic` solves a narrower coordination problem:

- keep raw training artifacts in the training repository;
- extract only compact factual evidence for GPT review;
- preserve the boundary between facts, decisions, and recommendations;
- archive each run review with stable paths and validation status;
- make future GPT-to-Codex tasks short, repeatable, and scope-controlled.

## What This Repository Is

This repository is:

- a portfolio entry point for an AI-assisted engineering workflow;
- a prompt-packaging and task-interface repository;
- a Codex skill contract repository;
- a compact training-analysis evidence archive;
- a history map for GPT tuning review and engineering retrospection.

The main tracked surfaces are:

- `.agents/skills/` for Codex execution contracts;
- `docs/` for GPT-facing interfaces, engineering facts, and writing rules;
- `training_results/current/` for the current factual analysis JSON;
- `training_results/history/` for paired historical analysis and GPT review
  records.

## What This Repository Is Not

This repository is not:

- the training implementation repository;
- a model-weight or checkpoint repository;
- a raw log or CSV dump;
- an automated training runner;
- a claim of global model superiority;
- a replacement for the external `DRL-path-finding` source repository.

The repository intentionally does not store full logs, full CSVs, checkpoints,
model weights, plots, trajectories, or binary artifacts. The `.gitignore`
also excludes common generated outputs such as `outputs/`, `checkpoints/`,
`*.pt`, `*.pth`, `*.ckpt`, `*.onnx`, `*.npy`, and `*.npz`.

## Repository Structure

| Path | Role |
| --- | --- |
| `README.md` | Public portfolio overview and reviewer guide. |
| `.agents/skills/training-run-factual-analysis/` | Codex factual extraction contract. |
| `.agents/skills/training-analysis-archive/` | Codex archive landing contract. |
| `docs/gpt_to_codex_prompting.md` | Prompt shell for turning GPT decisions into bounded Codex tasks. |
| `docs/gpt_tuning_review.md` | GPT tuning-review interface and decision boundaries. |
| `docs/training_system_manifest.md` | Engineering facts about the external training system. |
| `docs/document_authoring_principles.md` | Rules for compact, interface-first documentation. |
| `training_results/current/` | Current compact factual analysis output. |
| `training_results/history/history_index.json` | Compact index of archived single-run records. |
| `training_results/history/tuning_map.md` | Navigation map for tuning history and open uncertainty. |
| `training_results/history/single_runs/` | Per-run factual analysis and GPT tuning review archives. |

## Core Components

### GPT-Facing Interfaces

The `docs/` directory defines how GPT should reason from structured evidence:

- `gpt_tuning_review.md` separates evidence admission, baseline judgement,
  mechanism diagnosis, and next-action selection.
- `gpt_to_codex_prompting.md` defines a short task shell for passing work from
  GPT to Codex without duplicating the full skill procedure.
- `training_system_manifest.md` records implementation facts from the external
  training repository, including the stable launcher, train-side-only mode,
  reward knobs, learner/replay knobs, and artifact boundaries.

### Codex Skill Contracts

The `.agents/skills/` directory contains concrete Codex execution contracts:

- `training-run-factual-analysis` extracts compact factual evidence from one
  training run and writes `current_training_run_analysis.json`.
- `training-analysis-archive` archives one Codex factual JSON with one
  GPT-owned tuning review digest, then updates the compact history index and
  tuning map.

Both skills emphasize bounded writes, path sanitization, source integrity, and
decision boundaries. Codex records facts and preserves GPT-owned review
content; it does not invent tuning conclusions or modify training outputs.

### Structured Training Records

The `training_results/` tree contains compact records rather than raw
artifacts:

- `current/current_training_run_analysis.json` is a Codex-generated factual
  report for the current run.
- `history/history_index.json` currently indexes 24 paired single-run records.
- `history/tuning_map.md` summarizes the current navigation state, retained
  baseline context, refuted directions, supported directions, and open
  uncertainties.
- `history/single_runs/` stores paired `training_run_analysis.json` and
  `tuning_review.md` or legacy `tuning_review.json` files.

The tracked records show a workflow for preserving run context and review
decisions without publishing raw generated artifacts.

## GPT-to-Codex Workflow

The intended collaboration loop is:

1. The external training repository produces a run and source artifacts.
2. Codex reads the relevant artifacts and writes one compact factual JSON.
3. GPT reviews the factual JSON, history index, tuning map, and manifest.
4. GPT emits a bounded next action or a review digest.
5. Codex packages or archives the result through the selected skill contract.
6. The history index and tuning map keep later reviews anchored to prior
   evidence and prior decisions.

This separation is the main engineering idea of the repository:

- GPT owns interpretation and tuning decisions.
- Codex owns controlled execution, file writes, preservation, and validation.
- Raw training artifacts stay outside this repository.

## Codex Skills

`training-run-factual-analysis`

- Extracts structured train-side facts into a compact JSON report.
- Enforces schema control, privacy-aware paths, and no-decision reporting.

`training-analysis-archive`

- Archives one factual JSON with one GPT-owned review digest.
- Updates history navigation while enforcing output allowlists.

These skills package repeated research operations into explicit contracts that
an AI coding agent can follow safely.

## Training Analysis Records

The current and historical records support several concrete review tasks:

- comparing endpoint metrics, late-stage deltas, behavior diagnostics, reward
  breakdowns, and learner/replay context;
- tracking whether a GPT review supported, partially supported, or refuted a
  prior hypothesis;
- retaining a single-seed train-side reference baseline without claiming
  cross-seed or paper-level superiority;
- listing refuted tuning directions so GPT does not repeat old suggestions
  without new evidence;
- keeping the latest candidate surface visible without duplicating full
  historical prose.

The archive is useful for engineering retrospection, not for reproducing the
full training run. Reproduction remains the responsibility of the source
training repository.

## Related Repositories

`DRL-path-finding`

- Owns training code, runtime behavior, generated outputs, logs, CSVs,
  checkpoints, model weights, plots, trajectories, and exact artifact schemas.

`Automatic`

- Owns prompt interfaces, Codex skills, compact evidence digests, history
  index, tuning map, and portfolio documentation.

`agent_build`

- Not represented by tracked source files or integration contracts here.
- If used alongside this work, it should remain a separate agent or
  application workstream.

## Current Status and Limitations

Current status:

- repository is organized as a documentation and evidence-control plane;
- two concrete Codex skills are tracked;
- four supporting documents are tracked under `docs/`;
- current factual analysis is stored under `training_results/current/`;
- 24 historical single-run records are indexed under `training_results/history/`.

Limitations:

- this repository does not contain the training implementation;
- it does not include checkpoints, model weights, raw logs, full CSVs, plots,
  trajectories, or binary outputs;
- the training-analysis history is single-seed and train-side oriented;
- archived review records are evidence for workflow and retrospection, not a
  global performance claim;
- older historical files may retain the earlier internal name `Automatic_2`.

## Internship Skill Mapping

AI-assisted R&D workflow design:

- The GPT/Codex role split is visible across `docs/`, `.agents/skills/`, and
  `training_results/`.

Prompt engineering:

- `docs/gpt_to_codex_prompting.md` defines concise task shells, fixed fields,
  and validation lines.

Codex task packaging:

- `.agents/skills/` turns repeated operations into explicit execution
  contracts.

Structured task decomposition:

- Factual extraction, GPT review, archive landing, history indexing, and
  tuning-map sync are separated.

Training analysis documentation:

- `training_results/current/` and `training_results/history/` store compact
  evidence digests and review records.

Engineering collaboration:

- GPT owns interpretation; Codex owns bounded writes, validation, and
  preservation.

Technical documentation organization:

- `docs/document_authoring_principles.md` defines compact, interface-first
  documentation rules.

Result archiving and retrospective review:

- `history_index.json`, `tuning_map.md`, and per-run archives preserve prior
  decisions and refuted directions.

## How To Review This Repository

For a fast review, read these files in order:

1. `README.md`
2. `docs/gpt_to_codex_prompting.md`
3. `.agents/skills/training-run-factual-analysis/SKILL.md`
4. `.agents/skills/training-analysis-archive/SKILL.md`
5. `training_results/history/tuning_map.md`

This path shows the problem, the GPT-to-Codex handoff, the Codex execution
contracts, and the archived evidence trail.
