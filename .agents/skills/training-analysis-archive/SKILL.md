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

Default paths:

- `source_analysis_json`: `training_results/current/current_training_run_analysis.json`
- `history_index_path`: `training_results/history/history_index.json`
- `tuning_map_path`: `training_results/history/tuning_map.md`

## 2. Contract

Archive one single-run Codex factual analysis JSON with one GPT-provided `tuning_review.md` digest.

Codex owns:

- Validate input fields and archive id uniqueness.
- Preserve one Codex factual analysis JSON without changing its meaning.
- Preserve one GPT-provided `tuning_review.md` digest without changing its meaning.
- Validate digest markers, headings, required fields, enums, single-run status semantics, and command format.
- Write the archive pair.
- Update `training_results/history/history_index.json` as a compact locator and status index.
- Sync `training_results/history/tuning_map.md` from GPT digest fields only.
- Report compact validation and write status.

Authority boundary:

- Do not reanalyze training outputs.
- Do not inspect factual metrics to decide archive content or tuning map content.
- Do not infer baseline status, selected surface, validation status, tuning recommendation, or next hyperparameters.
- Do not decide stop, continue, branch, method conclusion, paper conclusion, accepted baseline, or performance superiority.
- Do not summarize, expand, compress, rewrite, reinterpret, or otherwise change GPT digest meaning.

## 3. Inputs

| Field | Required | Contract |
| --- | --- | --- |
| `archive_type` | yes | Must be `single_run`. |
| `archive_id` | yes | Unique single-run archive id. |
| `source_analysis_json` | yes | Path or supplied JSON object; source `report_type` must be `training_run_factual_analysis`. |
| `tuning_review_md_digest` | yes | GPT digest between required begin and end markers. |
| `history_index_path` | yes | Path for `training_results/history/history_index.json`. |
| `tuning_map_path` | yes | Path for `training_results/history/tuning_map.md`. |

Input discipline:

- Treat `history_index_path` and `tuning_map_path` as repository-relative tracked paths.
- Use repository-relative paths in tracked history payloads.
- Store no private absolute paths in tracked history payloads.

## 4. Digest Contract

Marker rules:

| Rule | Required value |
| --- | --- |
| `BEGIN_TUNING_REVIEW_MD_DIGEST` exists | yes |
| `END_TUNING_REVIEW_MD_DIGEST` exists | yes |
| Markers are on own unindented lines | yes |
| Exactly one digest block | yes |
| Digest is wrapped in a fenced code block | no |
| Literal triple-backtick sequence inside digest | forbidden |
| Marker lines are written to `tuning_review.md` | no |

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

Preservation rules:

- Extract only the marker body.
- Exclude marker lines from written `tuning_review.md`.
- Preserve the digest body exactly except for removing the enclosing marker lines.
- Do not invent hypotheses, evidence, commands, validation focus, recommendations, baseline values, or tuning decisions.

## 5. Archive Writes

Output allowlist:

- `training_results/history/single_runs/<archive_id>/training_run_analysis.json`
- `training_results/history/single_runs/<archive_id>/tuning_review.md`
- `training_results/history/history_index.json`
- `training_results/history/tuning_map.md`

Write sequence:

1. Validate `archive_type`, input presence, and `archive_id` uniqueness.
2. Parse `source_analysis_json`.
3. Validate source `report_type=training_run_factual_analysis`.
4. Extract and validate the GPT digest body.
5. Create `training_results/history/single_runs/<archive_id>`.
6. Write preserved `training_run_analysis.json`.
7. Write `tuning_review.md` from the digest body only.
8. Append or create the compact `history_index.json` entry.
9. Sync `tuning_map.md` from GPT digest fields only.
10. Validate diff scope and output allowlist.

Artifact discipline:

- Do not create `archive_manifest.json`.
- Do not copy raw artifacts, checkpoints, model weights, full logs, full CSVs, plots, trajectories, or binary artifacts.
- Do not modify training source code, training outputs, checkpoints, model weights, logs, CSVs, plots, trajectories, or existing archive payloads.

## 6. Index Density Contract

Required `history_index.json` root if missing:

| Field | Value |
| --- | --- |
| `schema_version` | `1.0` |
| `index_type` | `training_analysis_history_index` |
| `entries` | `[]` |

Single-run index entry fields:

| Field | Value source |
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
| `recommendation_type` | GPT digest field |
| `prior_validation_status` | GPT digest field |
| `validation_status` | GPT digest field |
| `baseline_update_status` | GPT digest field |
| `current_train_side_reference_baseline_before` | GPT digest field |
| `baseline_candidate` | GPT digest field |
| `current_train_side_reference_baseline_after` | GPT digest field |
| `baseline_scope` | GPT digest field |
| `selected_next_surface` | GPT digest field |
| `parameter_change` | GPT digest field |
| `recommended_next_run_name` | GPT digest field |
| compact validation fields | status labels for source parse, digest validation, semantic checks, write scope, and artifact guard |

Index must remain a compact locator and validation index.

Index stores:

- Repository-relative paths.
- Pairing status.
- GPT-owned compact decision fields listed above.
- Compact validation fields.

Index must not store:

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

Stop on an existing `archive_id` unless explicit replacement authorization exists.

## 7. Tuning Map Sync Contract

`training_results/history/tuning_map.md` is a compact current navigation summary, not decision authority and not a second history narrative.

Sync rules:

- Sync only from GPT digest fields.
- Do not inspect factual metrics.
- Do not infer missing digest values.
- Do not create recommendations.
- Do not duplicate the full digest.
- Do not append repeated retained-baseline prose.
- De-duplicate by direction or surface where practical.
- Keep each archive contribution compact.

Allowed map sections and update rules:

| Section | Contract |
| --- | --- |
| Current Train-Side Reference Baseline | Record only current reference baseline and basis from GPT baseline fields. |
| Tuning Path Summary | Append one compact row per archive. |
| Refuted Directions | Group or update by direction instead of endlessly duplicating rows. |
| Supported / Retained Directions | Keep only currently useful navigation signal. |
| Open Uncertainties | Merge overlapping `remaining_uncertainty` and `target_uncertainty` text when practical. |
| Next Candidate Surfaces | Keep the latest active candidate plus necessary pending or conditional surfaces. |

Map boundaries:

- No global optimum claims.
- No cross-seed superiority claims.
- No paper-level superiority claims.
- No tuning completion claims.
- No raw metrics dump.
- No full digest duplication.

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
- `diff_scope_check`

Validation rules:

- Confirm first-line YAML frontmatter remains valid when editing this skill.
- Confirm `archive_type=single_run`.
- Confirm `archive_id` does not already exist.
- Confirm source JSON parses and has `report_type=training_run_factual_analysis`.
- Confirm digest markers, headings, required fields, enums, status semantics, and command format.
- Confirm archive writes match the output allowlist.
- Confirm tracked history payloads use repository-relative paths and no private absolute paths.
- Confirm `history_index.json` remains compact and excludes forbidden dense fields.
- Confirm `tuning_map.md` is synced only from GPT digest fields.
- Confirm diff scope contains only authorized archive outputs for archive execution.

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

- Required input is missing, inaccessible, ambiguous, or unparsable.
- `archive_type` is not `single_run`.
- `archive_id` already exists without explicit replacement authorization.
- Source JSON parse or `report_type` validation fails.
- Digest marker, heading, field, enum, status semantics, command, or baseline contract validation fails.
- Requested writes exceed the output allowlist.
- Training source code, training outputs, existing archives, checkpoints, weights, logs, CSVs, plots, trajectories, or binaries would be modified.
- Any raw artifact, checkpoint, model weight, full log, full CSV, plot, trajectory, or binary artifact would be copied.
- `archive_manifest.json` would be created.
- Codex would need to infer, reinterpret, summarize, compress, expand, rewrite, or decide GPT-owned digest content.
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
