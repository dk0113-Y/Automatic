---
name: training-analysis-archive
description: Archive single-run training-analysis records into Automatic_2 history. Preserve Codex factual analysis JSON and GPT-provided tuning_review.md digest, validate baseline and tuning-decision fields, update history_index.json, sync tuning_map.md from GPT fields only, and never reanalyze training outputs or provide tuning decisions.
---

# Training Analysis Archive

## 1. Contract

Archive one single-run factual analysis JSON with one GPT-provided `tuning_review.md` digest.

Codex owns:
- Preserve Codex factual JSON.
- Preserve GPT `tuning_review.md` digest.
- Validate digest markers, headings, fields, enums, status semantics, and command format.
- Write the archive pair.
- Update `training_results/history/history_index.json`.
- Sync `training_results/history/tuning_map.md` from GPT digest fields only.

Codex must not:
- Reanalyze training outputs.
- Provide tuning decisions.
- Infer baseline.
- Infer selected surface.
- Invent next hyperparameters.
- Rewrite GPT digest meaning.
- Decide performance superiority, stop/continue/branch status, method conclusions, paper conclusions, or accepted baseline.

## 2. Inputs

| Field | Required | Contract |
| --- | --- | --- |
| `archive_type` | yes | `single_run` only. |
| `archive_id` | yes | Unique history archive id. |
| `source_analysis_json` | yes | Path or supplied JSON object; source `report_type` must be `training_run_factual_analysis`. |
| `tuning_review_md_digest` | yes | GPT digest between `BEGIN_TUNING_REVIEW_MD_DIGEST` and `END_TUNING_REVIEW_MD_DIGEST`. |
| `history_index_path` | no | Default: `training_results/history/history_index.json`. |
| `tuning_map_path` | no | Default: `training_results/history/tuning_map.md`. |

Expected current source path:
- `training_results/current/current_training_run_analysis.json`

## 3. Digest Contract

Marker rules:

| Rule | Required |
| --- | --- |
| `BEGIN_TUNING_REVIEW_MD_DIGEST` exists | yes |
| `END_TUNING_REVIEW_MD_DIGEST` exists | yes |
| Markers are on own unindented lines | yes |
| Exactly one digest block | yes |
| Digest is not wrapped in a fenced code block | yes |
| Literal triple-backtick sequence inside digest | no |

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
| Summary | `source_run_name`, `source_archive_id`, `recommendation_type`, `prior_validation_status`, `validation_status`, `baseline_update_status`, `current_train_side_reference_baseline_before`, `baseline_candidate`, `current_train_side_reference_baseline_after`, `baseline_scope`, `selected_next_surface`, `parameter_change`, `recommended_next_run_name`, `decision_summary` |
| Evidence Admission | `reproducible_launch_status`, `factual_summary_status`, `admission_result`, `note` |
| Prior Validation | `prior_hypothesis`, `validation_status`, `judgement`, `key_evidence`, `remaining_uncertainty` |
| Current Evidence Digest | `train_side_primary`, `mechanism_summary`, `posthoc_context`, `final_probe_supplemental`, `main_limitation` |
| Next Action | `target_uncertainty`, `next_hypothesis`, `selected_next_surface`, `parameter_change`, `rationale`, `command`, `expected_validation_focus` |
| Boundaries | `reference_baseline_note`, `evidence_boundary`, `redesign_boundary` |

Single-run status semantics:
- `prior_validation_status` records the current GPT review's validation of the immediately preceding hypothesis.
- `prior_validation_status` must match `validation_status`.
- Do not use `prior_validation_status` to store the previous archived run's validation status.

Enum validation:

| Field | Allowed values |
| --- | --- |
| `recommendation_type` | `next_run_plan`, `hold_current_baseline`, `requires_more_evidence`, `repeat_or_repair_run`, `method_redesign_discussion_only` |
| `prior_validation_status`, `validation_status` | `supported`, `partially_supported`, `refuted`, `inconclusive`, `not_applicable`, `blocked_by_evidence_gate` |
| `baseline_update_status` | `updated`, `unchanged`, `unresolved` |
| `baseline_scope` | `single_seed_train_side_reference_only` |

Command validation when `recommendation_type = next_run_plan`:
- `command` contains one launcher command line as plain text or indented plain text.
- Command starts with `.\scripts\launch_formal_train_stable.ps1`.
- Command does not include `cd`, local absolute paths, working-directory placeholders, or literal triple-backtick sequences.
- Command is not stored in `history_index.json`.

Preservation rules:
- Extract only marker body.
- Exclude markers from `tuning_review.md`.
- Preserve GPT digest meaning.
- Do not summarize, expand, compress, rewrite, reinterpret, reanalyze, change `recommendation_type`, invent hypotheses, invent evidence, invent commands, invent validation focus, or invent tuning decisions.

## 4. Archive Writes

Output allowlist:
- `training_results/history/single_runs/<archive_id>/training_run_analysis.json`
- `training_results/history/single_runs/<archive_id>/tuning_review.md`
- `training_results/history/history_index.json`
- `training_results/history/tuning_map.md`

Never output:
- `archive_manifest.json`
- raw artifacts
- checkpoints
- model weights
- full logs
- full CSVs
- plots
- trajectories
- binary artifacts

Execution steps:
1. Validate `archive_type` and inputs.
2. Check `archive_id` conflict.
3. Parse `source_analysis_json`.
4. Validate source `report_type`.
5. Extract digest marker body.
6. Validate digest headings, fields, enums, status semantics, and command.
7. Create archive directory.
8. Write preserved `training_run_analysis.json`.
9. Write `tuning_review.md` from marker body only.
10. Append compact `history_index.json` entry.
11. Sync `tuning_map.md` from GPT digest fields only.
12. Validate diff scope and forbidden artifacts.
13. Report result.

## 5. Index And Tuning Map Update

Required `history_index.json` root if missing:
- `schema_version`: `1.0`
- `index_type`: `training_analysis_history_index`
- `entries`: empty list

Single-run entry fields:

| Field | Value |
| --- | --- |
| `archive_id` | `<archive_id>` |
| `archive_type` | `single_run` |
| `run_name_or_group` | `<source_run_name>` |
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
| `validation.source_analysis_json_parse` | `passed` |
| `validation.tuning_review_md_digest_validation` | `passed` |
| `validation.single_run_status_semantics` | `passed` |
| `validation.no_training_repo_modification` | `true` |
| `validation.no_source_run_output_modification` | `true` |
| `validation.no_forbidden_artifacts_copied` | `true` |

Index compactness:
- Use repository-relative paths only.
- Store no private absolute paths.
- Do not duplicate digest content.
- Do not store `key_evidence`, `next_hypothesis`, `expected_validation_focus`, full command, full evidence details, historical trajectory, or current evidence interpretation.
- Stop on existing `archive_id` unless explicit replacement authorization exists.

`tuning_map.md` sync contract:
- Update `tuning_map.md` only after archive write and `history_index.json` update are otherwise valid.
- Use GPT digest fields only.
- Do not inspect factual metrics to decide map content.
- Do not infer `baseline_update_status`.
- Do not infer `validation_status`.
- Do not infer `selected_next_surface`.
- Do not create tuning recommendations.
- Do not add conclusions not present in GPT digest.

Allowed `tuning_map.md` updates:

| Section | Allowed update |
| --- | --- |
| Current Train-Side Reference Baseline | If `baseline_update_status=updated`, set current reference to `current_train_side_reference_baseline_after`. If `baseline_update_status=unchanged`, retain `current_train_side_reference_baseline_after` and record retained status. If `baseline_update_status=unresolved`, record unresolved without changing baseline. |
| Tuning Path Summary | Append one compact row for `archive_id` using `validation_status`, `baseline_update_status`, and `recommended_next_run_name`. |
| Refuted Directions | Add or update a compact row only when `validation_status=refuted` and digest states the direction in `parameter_change` or `decision_summary`. |
| Supported / Retained Directions | Add or update a compact row when `validation_status=supported` or `baseline_update_status=updated` / `unchanged`. |
| Open Uncertainties | Use `remaining_uncertainty` or `target_uncertainty` from digest. |
| Next Candidate Surfaces | Use `selected_next_surface` and `recommended_next_run_name` from digest. |

`tuning_map.md` style:
- Compact tables or short field lists.
- No full digest duplication.
- No raw metrics dump.
- No raw artifact references.
- No global optimum, cross-seed superiority, paper-level superiority, or tuning completion claims.
- Keep `tuning_map.md` as navigation summary only, not decision authority.

Tracked history payloads:
- Use repository-relative paths only.
- Store no private absolute paths.

## 6. Validation And Blockers

Required validation:
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

Blocker statuses:
- `blocked_insufficient_input`
- `archive_id_conflict`
- `invalid_archive_type`: Block when `archive_type` is not `single_run`.
- `parse_failed`
- `digest_validation_failed`
- `single_run_status_semantics_failed`: Block when `prior_validation_status` stores a previous archived run's status or differs from `validation_status`.
- `forbidden_scope_detected`
- `history_index_update_failed`
- `tuning_map_update_failed`
- `baseline_field_missing`
- `digest_baseline_contract_failed`

Forbidden actions:
- No training-output reanalysis.
- No training code modification.
- No source run output modification.
- No forbidden artifact copying.
- No `archive_manifest.json`.
- No tuning recommendations by Codex.
- No next hyperparameters invented by Codex.
- No baseline inference by Codex.
- No selected surface inference by Codex.
- No accepted-baseline decision by Codex.
- No stop/continue/branch decision by Codex.
- No method-level or paper-level conclusion.
- No rewriting GPT digest meaning.
- No private absolute paths in tracked history payloads.

## 7. Final Report

Report fields:
- `archive_type`
- `archive_id`
- `files_written`
- `history_index_path`
- `tuning_map_path`
- `source_analysis_preserved`
- `tuning_review_md_digest_preserved`
- `single_run_status_semantics_check`
- `digest_validation_result`
- `markers_excluded_from_tuning_review_md`
- `history_index_updated`
- `tuning_map_updated`
- `baseline_update_status_recorded`
- `current_train_side_reference_baseline_after_recorded`
- `selected_next_surface_recorded`
- `parameter_change_recorded`
- `no_archive_manifest_created`
- `no_forbidden_artifacts_copied`
- `no_tuning_decision_by_codex`
- `validation_results`
- `commit_push_status`
- `commit_hash`
- `push_target`
- `remaining_risks`
