---
name: training-analysis-archive
description: Archive single-run training-analysis records into Automatic_2 history.
---

# Training Analysis Archive

## 1. Metadata

| Field | Value |
| --- | --- |
| Skill name | `training-analysis-archive` |
| Task type | `single_run_analysis_archive_task` |
| Archive type | `single_run` only |
| Archive root | `training_results/history/single_runs/<archive_id>` |

## 2. Contract

Archive one single-run Codex factual JSON with one GPT-owned `tuning_review.md` digest.

Execution contract:

| Step | Rule |
| --- | --- |
| Preserve | One Codex factual analysis JSON; one GPT digest, with meaning unchanged. |
| Validate | Inputs, archive id, digest markers, headings, fields, enums, status semantics, command. |
| Write | Archive pair only: `training_run_analysis.json` and `tuning_review.md`. |
| Update | `training_results/history/history_index.json` as a compact locator/index. |
| Sync | `training_results/history/tuning_map.md` from GPT digest fields only. |
| Report | Compact validation, write, scope, commit, risk fields. |

Authority boundary:

| Codex does not | Scope |
| --- | --- |
| Reanalyze | Training outputs or factual metrics for archive/map decisions. |
| Infer | Baseline status, selected surface, validation status, tuning recommendation, next hyperparameters. |
| Decide | Stop/continue/branch, method conclusion, paper conclusion, accepted baseline, performance superiority. |
| Alter digest meaning | Summarize, expand, compress, rewrite, reinterpret, or change GPT digest content. |

## 3. Inputs

| Field | Required | Contract |
| --- | --- | --- |
| `archive_type` | yes | Must be `single_run`. |
| `archive_id` | yes | Unique single-run archive id. |
| `source_analysis_json` | yes | Path or JSON object; `report_type=training_run_factual_analysis`. |
| `tuning_review_md_digest` | yes | GPT digest between required markers. |
| `history_index_path` | yes | `training_results/history/history_index.json`. |
| `tuning_map_path` | yes | `training_results/history/tuning_map.md`. |

Path rule: tracked history payloads use repository-relative paths and no private absolute paths.

## 4. Digest Contract

Marker rules:

| Rule | Required |
| --- | --- |
| `BEGIN_TUNING_REVIEW_MD_DIGEST` exists | yes |
| `END_TUNING_REVIEW_MD_DIGEST` exists | yes |
| Markers on own unindented lines | yes |
| Exactly one digest block | yes |
| Fenced code wrapper | no |
| Literal triple-backtick sequence inside digest | forbidden |
| Marker lines written to `tuning_review.md` | no |

Required headings:

- `# GPT Tuning Review Digest`
- `## 1. Summary`
- `## 2. Evidence Admission`
- `## 3. Prior Validation`
- `## 4. Current Evidence Digest`
- `## 5. Next Action`
- `## 6. Boundaries`

Required fields:

| Section | Fields |
| --- | --- |
| Summary | `source_run_name`, `source_archive_id`, `recommendation_type`, `prior_validation_status`, `validation_status` |
| Summary | `baseline_update_status`, `current_train_side_reference_baseline_before`, `baseline_candidate` |
| Summary | `current_train_side_reference_baseline_after`, `baseline_scope`, `selected_next_surface` |
| Summary | `parameter_change`, `recommended_next_run_name`, `decision_summary` |
| Evidence Admission | `reproducible_launch_status`, `factual_summary_status`, `admission_result`, `note` |
| Prior Validation | `prior_hypothesis`, `validation_status`, `judgement`, `key_evidence`, `remaining_uncertainty` |
| Current Evidence Digest | `train_side_primary`, `mechanism_summary`, `posthoc_context`, `final_probe_supplemental`, `main_limitation` |
| Next Action | `target_uncertainty`, `next_hypothesis`, `selected_next_surface`, `parameter_change`, `rationale` |
| Next Action | `command`, `expected_validation_focus` |
| Boundaries | `reference_baseline_note`, `evidence_boundary`, `redesign_boundary` |

Enum validation:

| Field | Allowed values |
| --- | --- |
| `recommendation_type` | `next_run_plan`, `hold_current_baseline`, `requires_more_evidence`, `repeat_or_repair_run`, `method_redesign_discussion_only` |
| `prior_validation_status`, `validation_status` | `supported`, `partially_supported`, `refuted`, `inconclusive`, `not_applicable`, `blocked_by_evidence_gate` |
| `baseline_update_status` | `updated`, `unchanged`, `unresolved` |
| `baseline_scope` | `single_seed_train_side_reference_only` |

Single-run status semantics:

- `prior_validation_status` records the current GPT review's validation of the immediately preceding hypothesis.
- `prior_validation_status` must match `validation_status`.
- Do not use `prior_validation_status` to store the previous archived run's validation status.

Command validation when `recommendation_type=next_run_plan`:

- `command` contains one launcher command line as plain text or indented plain text.
- `command` starts with `.\scripts\launch_formal_train_stable.ps1`.
- `command` does not include `cd`.
- `command` does not include local absolute paths.
- `command` does not include working-directory placeholders.
- `command` does not include literal triple-backtick sequences.
- `command` is not stored in `history_index.json`.

Preservation:

- Extract marker body only.
- Exclude marker lines from written `tuning_review.md`.
- Preserve digest body exactly except marker removal.
- Do not invent hypotheses, evidence, commands, validation focus, recommendations, baselines, or tuning decisions.

## 5. Archive Writes

Output allowlist:

- `training_results/history/single_runs/<archive_id>/training_run_analysis.json`
- `training_results/history/single_runs/<archive_id>/tuning_review.md`
- `training_results/history/history_index.json`
- `training_results/history/tuning_map.md`

Write sequence:

1. Validate `archive_type`, inputs, and `archive_id` uniqueness.
2. Parse `source_analysis_json`; require `report_type=training_run_factual_analysis`.
3. Extract and validate GPT digest body.
4. Create `training_results/history/single_runs/<archive_id>`.
5. Write preserved `training_run_analysis.json`.
6. Write `tuning_review.md` from digest body only.
7. Append or create compact `history_index.json` entry.
8. Sync `tuning_map.md` from GPT digest fields only.
9. Validate diff scope and output allowlist.

Scope guard:

- Do not create `archive_manifest.json`.
- Do not copy raw artifacts, checkpoints, model weights, full logs, full CSVs, plots, trajectories, or binary artifacts.
- Do not modify training code/outputs, checkpoints, weights, logs, CSVs, plots, trajectories, or existing archives.

## 6. Index Density Contract

Required `history_index.json` root if missing:

| Field | Value |
| --- | --- |
| `schema_version` | `1.0` |
| `index_type` | `training_analysis_history_index` |
| `entries` | `[]` |

Single-run index fields:

| Field | Source |
| --- | --- |
| `archive_id` | input |
| `archive_type` | `single_run` |
| `run_name_or_group` | GPT `source_run_name` |
| `created_by` | `codex` |
| `tuning_review_generated_by` | `gpt` |
| `pairing_status` | `paired` |
| `training_analysis_path` | `training_results/history/single_runs/<archive_id>/training_run_analysis.json` |
| `tuning_review_path` | `training_results/history/single_runs/<archive_id>/tuning_review.md` |
| `source_current_analysis_path` | `training_results/current/current_training_run_analysis.json` |
| `source_report_type` | `training_run_factual_analysis` |
| `tuning_review_report_type` | `gpt_tuning_review_digest` |
| `recommendation_type` | GPT digest |
| `prior_validation_status` | GPT digest |
| `validation_status` | GPT digest |
| `baseline_update_status` | GPT digest |
| `current_train_side_reference_baseline_before` | GPT digest |
| `baseline_candidate` | GPT digest |
| `current_train_side_reference_baseline_after` | GPT digest |
| `baseline_scope` | GPT digest |
| `selected_next_surface` | GPT digest |
| `parameter_change` | GPT digest |
| `recommended_next_run_name` | GPT digest |
| compact validation fields | source parse, digest validation, semantics, write scope, artifact guard |

Index stores:

- Repository-relative paths.
- Pairing status.
- Listed GPT-owned compact fields.
- Compact validation fields.

Index excludes:

- Full digest content.
- `key_evidence`.
- `next_hypothesis`.
- `expected_validation_focus`.
- Full command.
- Full evidence details.
- Historical trajectory prose.
- Current evidence interpretation.
- Raw metrics.
- Private absolute paths.

Stop on existing `archive_id` unless replacement is explicitly authorized.

## 7. Tuning Map Sync Contract

`training_results/history/tuning_map.md` is compact current navigation only.

Map role:

- Not a full chronological history.
- Not a second tuning review.
- Not decision authority.
- No full GPT digest duplication.
- No `history_index.json` duplication.

Required map sections:

1. `# Tuning Map`
2. `## 1. Scope`
3. `## 2. Current Train-Side Reference Baseline`
4. `## 3. Active Tuning Branch`
5. `## 4. Tuning Path Summary`
6. `## 5. Refuted Directions`
7. `## 6. Supported / Retained Directions`
8. `## 7. Open Uncertainties`
9. `## 8. Next Candidate Surfaces`
10. `## 9. Maintenance Rule`

Sync rules:

- Sync only from GPT digest fields.
- Do not inspect factual metrics.
- Do not infer missing digest values.
- Do not create recommendations.
- De-duplicate by direction/surface where practical.
- Keep each archive contribution compact.

Allowed map updates:

| Section | Rule |
| --- | --- |
| Scope | Maximum 5 compact bullets. |
| Current Train-Side Reference Baseline | One table; short aggregate basis, no per-run retained-baseline prose. |
| Active Tuning Branch | One table; no run-by-run prose. |
| Tuning Path Summary | Short rows; avoid long next-run names except latest active candidate. |
| Refuted Directions | Group or update by direction. |
| Supported / Retained Directions | Aggregate repeated baseline-retention records. |
| Open Uncertainties | Merge overlapping `remaining_uncertainty` and `target_uncertainty`. |
| Next Candidate Surfaces | Latest active surface plus explicit pending/conditional surfaces only. |
| Maintenance Rule | Compact bullets only. |

Map excludes:

- Full digest content.
- `history_index.json` duplication.
- Global optimum claims.
- Cross-seed superiority claims.
- Paper-level superiority claims.
- Tuning completion claims.
- Raw metrics.
- Full command.
- Full evidence details.
- Per-run retained-baseline rows after each refuted/partial archive.
- Stale next-candidate surfaces already executed, refuted, or superseded.
- Repeated `epsend004 retained` prose per archive.

## 8. Validation

Required validation labels:

- `archive_type_valid`
- `archive_id_conflict_check`
- `source_json_parse`
- `source_report_type_check`
- `digest_marker_check`
- `digest_heading_check`
- `digest_required_field_check`
- `digest_enum_check`
- `single_run_status_semantics_check`
- `digest_command_check`
- `source_analysis_preserved`
- `tuning_review_md_digest_preserved`
- `history_index_update`
- `tuning_map_sync`
- `tuning_map_current_navigation_check`
- `tuning_map_retained_baseline_dedup_check`
- `tuning_map_next_surface_staleness_check`
- `diff_scope_check`

Validation rules:

- Confirm `archive_type=single_run` and unique `archive_id`.
- Confirm source JSON parses with `report_type=training_run_factual_analysis`.
- Confirm digest markers, headings, fields, enums, status semantics, and command format.
- Confirm writes match the output allowlist.
- Confirm tracked paths are repository-relative and non-private.
- Confirm `history_index.json` is compact and excludes dense fields.
- Confirm `tuning_map.md` is compact current navigation from GPT digest fields only.
- Confirm retained-baseline rows are aggregated instead of repeated per archive.
- Confirm next-candidate surfaces exclude executed, refuted, or superseded surfaces unless explicitly pending/conditional.
- Confirm diff scope matches authorized outputs.

## 9. Blockers

Required blocker labels:

- `blocked_insufficient_input`
- `archive_id_conflict`
- `invalid_archive_type`
- `parse_failed`
- `digest_validation_failed`
- `single_run_status_semantics_failed`
- `forbidden_scope_detected`
- `history_index_update_failed`
- `tuning_map_update_failed`
- `baseline_field_missing`
- `digest_baseline_contract_failed`

Block when:

- Input is missing, inaccessible, ambiguous, or unparsable.
- `archive_type` is not `single_run`.
- `archive_id` already exists without replacement authorization.
- Source parse or `report_type` check fails.
- Digest marker, heading, field, enum, status, command, or baseline validation fails.
- Writes exceed the output allowlist or scope guard.
- Codex would infer, decide, reinterpret, summarize, compress, expand, rewrite, or change GPT-owned content.
- Map sync would append another retained-baseline row when an aggregate row already exists.
- Map sync would list executed, refuted, or superseded next surfaces as active candidates.
- Map sync would require Codex to infer a missing GPT decision.
- Tracked history payloads would contain private absolute paths.

## 10. Final Report

Report compact fields only:

- `archive_type`
- `archive_id`
- `files_written`
- `validation_result`
- `digest_preserved`
- `history_index_updated`
- `tuning_map_updated`
- `scope_guard_status`
- `commit_push_status`
- `commit_hash`
- `push_target`
- `remaining_risks`
