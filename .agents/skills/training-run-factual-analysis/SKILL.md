---
name: training-run-factual-analysis
description: Train-side-only factual JSON.
---

# Training Run Factual Analysis

## 1. Metadata

| Field | Value |
| --- | --- |
| Skill name | `training-run-factual-analysis` |
| Task type | `training_run_factual_analysis_task` |
| Default output | `training_results/current/current_training_run_analysis.json` |

Constants:

- `schema_version=1.0`
- `report_type=training_run_factual_analysis`
- `generated_by=codex`
- `tuning_recommendation_provided=false`

## 2. Contract

Inputs:

- `source_run_dir`
- `run_name`
- optional `output_report_path`
- optional `destination_directory` only to resolve default output

Directives:

- Authorized write: `output_report_path` only.
- Default output: `training_results/current/current_training_run_analysis.json`.
- Output: compact GPT-review-facing factual JSON digest.
- Source artifacts remain the full evidence.
- Codex performs factual extraction only.
- GPT performs tuning review, baseline decision, mechanism diagnosis, and
  next-run decision.
- Train-side monitoring is the primary factual evidence.
- Use repository-relative paths or stable labels where practical.
- Do not write private absolute source paths into tracked payloads.
- Block insufficient input with `blocked_insufficient_input`.
- No tuning recommendation, next hyperparameters, baseline acceptance,
  stop/continue/branch decision, method conclusion, or paper conclusion.
- No performance superiority decision.
- No artifact copying, training source/output writes, checkpoints, model
  weights, full logs, full CSVs, plots, trajectories, raw outputs, or binaries.

## 3. Evidence Sources

| Artifact | Evidence role |
| --- | --- |
| `logs/reproducibility_contract.json` | Launch/reproducibility contract |
| `logs/metric_snapshot.json` | Train-side metric digest and statuses |
| `logs/config_snapshot.json` | Config/runtime/mode summary |
| `logs/artifact_index.json` | Presence and parseability metadata |
| `logs/train_steps.csv` | Source train-step monitoring; summarize only |
| `logs/train_episodes.csv` | Source train-episode monitoring; summarize only |
| launch/stdout/argv summaries if present | Sanitized command identity only |
| posthoc/final_probe artifacts | Status-only; not performance evidence |

posthoc/final_probe artifacts:

- `logs/posthoc_selection_summary.json`
- `logs/final_probe_summary.json`
- `logs/final_probe.csv`

## 4. Mode Handling

Current mainline:

- `train_side_only_tuning=true`

Skipped posthoc/final_probe under `train_side_only_tuning=true`:

- `status=skipped_train_side_only_tuning`
- `role=not_applicable_for_train_side_tuning`
- `missingness_treatment=not_missing`
- Intentional absence; not missing artifacts.

Unexpected posthoc/final_probe presence:

- `status=unexpected_present`
- `role=not_used_for_train_side_tuning`
- `missingness_treatment=not_missing`
- Presence/status only; not performance evidence.

False or unknown train-side-only mode:

- `train_side_only_tuning=false`: `unsupported_non_train_side_only_output`
- `train_side_only_tuning=unknown`: `unknown_mode`
- Do not trigger legacy metric extraction.

Summary readiness:

- `factual_summary_ready` is allowed when reproducibility and core train-side
  monitoring are ready, even when posthoc/final_probe are skipped under
  train-side-only mode.

Allowed mode/status values:

- `train_side_only_tuning`: `true`; `false`; `unknown`
- `test_side_evaluation_status`:
  `skipped_train_side_only_tuning`; `unsupported_non_train_side_only_output`;
  `unknown_mode`; `missing`; `malformed`
- `skipped_test_side_evaluation.reason`:
  compact source value; `train_side_only_tuning`; `unknown`
- `formal_final_object_role`:
  `not_applicable_for_train_side_tuning`; `not_used_for_train_side_tuning`;
  `unknown`
- `posthoc_selection.status`; `supplemental_final_probe.status`:
  `skipped_train_side_only_tuning`; `unexpected_present`;
  `unsupported_non_train_side_only_output`; `unknown_mode`; `missing`;
  `malformed`
- `posthoc_selection.role`; `supplemental_final_probe.role`:
  `not_applicable_for_train_side_tuning`; `not_used_for_train_side_tuning`;
  `unknown`
- `missingness_treatment`: `not_missing`; `missing`; `malformed`; `unknown`

Forbidden test-side content:

- No winner step, selected candidate steps, checkpoint winners, candidate
  rankings, final_probe summaries, best-vs-last summaries, held-out results,
  or test-side metrics.

## 5. Extraction Targets

Extraction order:
1. `reproducible_launch`
2. `train_side_mode_detection`
3. `train_side_monitoring`
4. `configuration_and_runtime`
5. `test_side_status_only`
6. `missingness_parseability`

`reproducible_launch_status`:

- `reproducible_launch_confirmed`
- `reproducible_launch_not_confirmed`
- `reproducible_launch_contradictory`
- `reproducibility_artifacts_missing`
- `reproducibility_unverified`

`reproducible_launch`:

- `contract_verdict`
- strict reproducibility status
- deterministic algorithm status
- `PYTHONHASHSEED`
- `CUBLAS_WORKSPACE_CONFIG`
- backend requested flags and runtime readbacks
- train seed status
- eval seed status only as mode/status metadata if present
- sanitized source branch/commit when available

`configuration_and_runtime`:

- Compact comparability/runtime summary: steps/episodes, budget, epsilon,
  replay/learner/update, reward, map/eval, mode/test-side status, skipped
  reason, device, duration, sanitized git identity, backend status, seed status.

`missingness_parseability`:

- Compact statuses: found/missing files, parse failures, malformed JSON,
  missing columns, empty CSVs, unverified items, skipped train-side-only
  artifacts, unexpected test-side presence, scope-skipped artifacts, sufficiency.

`factual_summary_status`:

- `factual_summary_ready`
- `partial_factual_summary`
- `missing_core_training_monitoring`
- `reproducibility_unverified`
- `blocked_insufficient_input`
- `parse_failed`

`train_side_monitoring`:

- `row_counts`: train step rows; train episode rows; empty CSV status; counts.
- `parseability`: readable/malformed files; NaN/nonfinite handling.
- `endpoint_metrics`: `reward`; `coverage`; `success_rate`;
  `episode_length`; `repeat_visit_ratio` / `RVR`; `timeout`.
- `initial_to_endpoint_delta`: compact start-to-end deltas.
- `late_stage_delta`: compact late-stage deltas/directions when safe.
- `key_behavior_diagnostics`: `zero_info`; `recent_revisit`; `stall`;
  `turn_ge_90`; `turn_135`; `turn_180`; `timeout_flag`;
  `turn_penalty_weight_sum` when present.
- `derived_exploration_efficiency`: `coverage_gain_per_step`;
  `weighted_info_gain_per_step`; `zero_info_rate`; `recent_revisit_rate`;
  `stall_rate`; `turn_burden_rate`; `timeout_rate`;
  `metric_role=diagnostic_only`;
  `evidence_role=not_standalone_superiority_evidence`.
- `reward_breakdown`: `info_reward_sum`; `step_penalty_sum`;
  `recent_revisit_penalty_sum`; `turn_penalty_sum`; `timeout_penalty_sum`;
  `terminal_bonus_sum`.
- `reward_event_summary`: `delta_empty_sum`; `delta_obstacle_sum`;
  `empty_info_gain_sum`; `obstacle_info_gain_sum`;
  `weighted_obstacle_info_gain_sum`; `weighted_info_gain_sum`;
  `obstacle_info_contribution_ratio`; `recent_revisit_trigger_count`;
  `stall_trigger_count`; `zero_info_step_count`; `turn_ge_90_count`;
  `turn_135_count`; `turn_180_count`; `timeout_flag`.
- `learner_replay_exploration_context`: `loss`; `q_mean`;
  `target_q_mean`; `td_abs_mean`; `grad_norm`; `replay_size`; `epsilon`;
  `batch_size`; `replay_capacity`; `n_step`; `gamma`; `learning_rate`;
  `target_update_interval`; `collect_steps_per_iter`;
  `learner_updates_per_iter`; `train_every_env_steps`.
- `missing_expected_columns`: absent required train-side columns.

## 6. JSON Schema And Density Contract

Required top-level keys:

- `schema_version`
- `report_type`
- `generated_by`
- `source_run_dir`
- `run_name`
- `files_inspected`
- `commands_run`
- `reproducible_launch_status`
- `reproducible_launch`
- `train_side_monitoring`
- `posthoc_selection`
- `supplemental_final_probe`
- `configuration_and_runtime`
- `missing_artifacts`
- `parse_failures`
- `unverified_items`
- `forbidden_artifact_findings`
- `factual_summary_status`
- `tuning_recommendation_provided`

Required `train_side_monitoring` keys:

- `row_counts`
- `parseability`
- `endpoint_metrics`
- `initial_to_endpoint_delta`
- `late_stage_delta`
- `key_behavior_diagnostics`
- `derived_exploration_efficiency`
- `reward_breakdown`
- `reward_event_summary`
- `learner_replay_exploration_context`
- `missing_expected_columns`

Density allowlist:

| Store | Do not store |
| --- | --- |
| Compact scalar values | Per-row arrays; per-episode arrays |
| Status labels and enums | Full CSV rows; full CSV headers |
| Small objects and key deltas | Full logs; full stdout/stderr |
| Short diagnostic summaries | Long command traces; broad numeric dumps |
| Stable identifiers and sanitized paths | Full artifact inventories; raw config dumps |
| Compact validation facts | Long tracebacks; raw outputs; binary artifacts |
| Presence/parse/status metadata | Plots; trajectories; checkpoints; model weights |

Field density rules:

- `files_inspected`: compact artifact-role map only.
  Include artifact label, presence, parse status, evidence role.
- `commands_run`: compact command metadata only.
  Include command label, purpose, exit status, short validation result.
  Exclude full command output.
- `missing_artifacts`: compact status list, not an audit report.
- `parse_failures`: compact status list, not long tracebacks.
- `unverified_items`: compact status list of unresolved evidence gaps.
- `configuration_and_runtime`: compact comparability/runtime summary,
  not a copied config snapshot.
- `derived_exploration_efficiency`: diagnostic-only;
  not standalone superiority evidence.
- JSON payload: optimized for GPT tuning review.
  Preserve tuning-relevant factual evidence and remove process noise.

## 7. Validation

- `schema_and_constants`: required keys and constants present;
  `tuning_recommendation_provided=false`.
- `mode_detection_contract`: allowed values only; false/unknown mode does not
  trigger legacy metric extraction.
- `train_side_monitoring_contract`: required train-side groups and endpoint
  metrics present or compactly marked unavailable.
- `derived_metrics_contract`: seven derived diagnostics plus
  `metric_role=diagnostic_only` and
  `evidence_role=not_standalone_superiority_evidence`.
- `test_side_status_only_contract`: posthoc/final_probe status, role, and
  missingness only; no metrics, winners, rankings, summaries, or evidence use.
- `factual_summary_status_contract`: ready status allowed with reproducibility
  and core train-side monitoring, including skipped train-side-only
  posthoc/final_probe.
- `density_contract`: compact digest only; no process noise or
  source-artifact dumps.
- `path_sanitization`: repository-relative paths or stable labels; no private
  absolute source paths.
- `write_scope`: write only `output_report_path`.
- `source_integrity`: no training code modification, source output
  modification, or artifact copying.
- `factual_only_boundary`: no tuning recommendation, next hyperparameters,
  baseline decision, stop/continue/branch decision, method conclusion,
  paper conclusion, or performance superiority decision.

## 8. Blockers

- `blocked_insufficient_input`: input missing, inaccessible, not a directory,
  ambiguous, or unparsable enough to start.
- `write_scope_violation`: write outside `output_report_path`.
- `source_integrity_violation`: training code or source run outputs modified.
- `artifact_copy_violation`: checkpoints, model weights, full logs, full CSVs,
  raw outputs, plots, trajectories, or binary artifacts copied.
- `path_privacy_violation`: tracked JSON contains private absolute source paths.
- `decision_scope_violation`: tuning, next-parameter, baseline, stop/continue,
  branch, method, paper, or superiority decisions included.
- `evidence_role_violation`: posthoc/final_probe used as performance evidence.
- `train_side_only_missingness_violation`: skipped posthoc/final_probe under
  `train_side_only_tuning=true` treated as missing.
- `output_density_violation`: JSON contains process noise or source-artifact
  dumps.
- `test_side_metric_extraction_violation`: posthoc/final_probe metrics,
  winners, rankings, summaries, held-out results, or test-side metrics
  extracted.

## 9. Final Report

Report compact fields:

- `output_report_path`
- `run_name`
- `source_run_dir_label`
- `reproducible_launch_status`
- `factual_summary_status`
- `train_side_only_tuning`
- `posthoc_selection_status`
- `supplemental_final_probe_status`
- `files_inspected_count`
- `commands_run_count`
- `validation_result`
- `blockers`
- `tuning_recommendation_provided`
