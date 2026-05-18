# GPT Tuning Review
Use this interface for GPT formal tuning review from the current single-run factual JSON and compact history context.

## 1. Inputs

Use:
- current factual report: `training_results/current/current_training_run_analysis.json`
- history index: `training_results/history/history_index.json`
- tuning map: `training_results/history/tuning_map.md`
- matched prior tuning review resolved from `history_index.json` by `recommended_next_run_name` when available
- engineering manifest: `docs/training_system_manifest.md`

Rules:
- Do not infer baseline from folder names.
- Do not treat the immediate prior run as the baseline unless history explicitly says so.
- Resolve current reference baseline from prior review fields or `tuning_map.md`.
- Use compact factual evidence only.
- Do not inspect full logs, full CSVs, checkpoints, model weights, raw outputs, plots, trajectories, or generated run artifacts.

## 2. Evidence Admission

Required gates:
- `reproducible_launch_status = reproducible_launch_confirmed`
- `factual_summary_status = factual_summary_ready`

`reproducible_launch.contract_verdict == strict_contract_ready` supports reproducible launch status; it is not a separate gate.

If either gate fails:
- `recommendation_type` must be `requires_more_evidence` or `repeat_or_repair_run`.
- Do not output `next_run_plan` command content.

Current train-side-only evidence rule:
- In current train-side-only factual reports, posthoc/final_probe objects are status-only and not performance evidence.

## 3. Review Contract

Evidence admission fields:
- `reproducible_launch_status`
- `factual_summary_status`
- `admission_result`
- `evidence_boundary`

Prior validation fields:
- `prior_hypothesis`
- `prior_validation_status` or `validation_status`
- `key_evidence`
- `remaining_uncertainty`

Baseline decision fields:
- `current_train_side_reference_baseline_before`
- `baseline_candidate`
- `baseline_update_status`
- `current_train_side_reference_baseline_after`
- `baseline_update_basis`
- `baseline_scope`

Mechanism diagnosis fields:
- `performance_change_summary`
- `primary_improvement_or_regression_factors`
- `mechanism_interpretation`
- `engineering_consistency_check`
- `uncertainty_remaining`

`prior_validation_status` / `validation_status` enum:
- `supported`
- `partially_supported`
- `refuted`
- `inconclusive`
- `not_applicable`
- `blocked_by_evidence_gate`

`baseline_update_status` enum:
- `updated`
- `unchanged`
- `unresolved`

`baseline_scope` enum:
- `single_seed_train_side_reference_only`

Baseline rules:
- Use `updated` only when admissible train-side endpoint metrics and key diagnostics support improvement over the current train-side reference baseline.
- Use `unchanged` when the current run fails to outperform the reference baseline or only improves relative to an already-refuted run.
- Use `unresolved` when the baseline cannot be located without unsafe inference.
- Reward-scale changes require caution: reward increases caused by reward coefficient changes must not override weaker coverage, success_rate, timeout, or core task-completion metrics.
- Runtime speed alone is not method-performance superiority.

## 4. Next Action Contract

`recommendation_type` enum:
- `next_run_plan`
- `hold_current_baseline`
- `requires_more_evidence`
- `repeat_or_repair_run`
- `method_redesign_discussion_only`

Next action fields:
- `recommendation_type`
- `selected_next_surface`
- `target_uncertainty`
- `next_hypothesis`
- `parameter_change`
- `evidence_rationale`
- `expected_validation_focus`

Decision rules:
- Use `next_run_plan` only when gates pass and evidence supports one concrete bounded performance-improvement run.
- Keep one primary parameter or one tightly coupled mechanism group per `next_run_plan`.
- Use `tuning_map.md` to avoid repeating refuted directions without explicit new evidence.
- Use `docs/training_system_manifest.md` to distinguish launcher-ready, config/implementation, and method-level surfaces.
- If launcher-ready, output exactly one launcher-only PowerShell command.
- Each formal train-side launcher command must start with `.\scripts\launch_formal_train_stable.ps1`.
- Each formal train-side launcher command must include `-TrainSideOnlyTuning:$true`.
- If the selected change is not launcher-ready, output a bounded implementation/config task instead of inventing a launcher flag.
- Do not default to robustness confirmation after a useful single-seed train-side result.
- Recommendations must bind parameter/config/reward change to evidence and a testable hypothesis.

## 5. Output Format

Use exactly these Chinese user-facing sections:
1. 证据定位与准入结论
2. 上一轮假设验证
3. 基线更新判断
4. 系统性能变化原因
5. 下一轮调参决策
6. 下一轮训练命令
7. 边界与风险

Each output must explicitly include:
- `recommendation_type`
- `prior_validation_status` or `validation_status`
- `current_train_side_reference_baseline_before`
- `baseline_candidate`
- `baseline_update_status`
- `current_train_side_reference_baseline_after`
- `baseline_update_basis`
- `baseline_scope`
- `target_uncertainty`
- `next_hypothesis`
- `selected_next_surface`
- `parameter_change`
- `evidence_rationale`
- `expected_validation_focus`

Command format:
- If `recommendation_type = next_run_plan` and the selected change is launcher-ready, output exactly one standalone command in a `text` fenced block.
- The command block must contain no `cd`, local absolute paths, placeholders, explanatory paragraphs, or nested fenced blocks.
- If no launcher command is allowed, explicitly state why no command is provided.

Do not output:
- Codex prompt
- archive digest
- `tuning_review_payload` JSON
- source-code modification task
- history update task

## 6. Boundaries

- Train-side monitoring is primary evidence.
- posthoc/final_probe are status-only under current train-side-only factual reports and are not performance evidence.
- Baseline update is not a global optimum, cross-seed conclusion, paper-level conclusion, method superiority claim, or tuning completion claim.
- Method-level redesign requires explicit user approval.
- Do not inspect or rely on full logs, full CSVs, checkpoints, model weights, raw outputs, plots, trajectories, binary artifacts, or generated run artifacts.
- Do not perform factual extraction, archive/landing execution, source-code edits, or artifact copying.
