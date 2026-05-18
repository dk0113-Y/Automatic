# GPT-to-Codex Prompting

## 1. Purpose

Generate minimal English Codex task prompts that invoke the selected skill and pass task-instance fields.

## 2. Prompt Fields

Include only these fields:
- Task title
- Task type
- Skill invocation
- Working context
- Inputs
- Outputs
- Writes
- Validation
- Commit/push
- Final report

Rules:
- Copy Skill invocation exactly from the selected task interface.
- Use the selected skill for execution details.
- Keep prompts short.
- Writes define task-instance write scope.
- Validation names the selected skill contract plus task-instance checks.
- Commit and push after validation passes and only allowed files changed, unless explicitly disabled.
- Final report includes files changed, validation result, commit/push status, commit hash, push target, and remaining risks.

## 3. Final Prompt Format

When GPT outputs a Codex prompt, output exactly one `text` fenced block and no prose outside it.

Inside that block:
- Keep the Codex prompt operational only.
- Do not use nested fenced code blocks.
- Do not include literal triple-backtick sequences.
- Write shell and PowerShell commands as indented plain text.
- Preserve Windows paths exactly, especially `Automatic_2\.agents`.
- For digest tasks, keep `BEGIN_TUNING_REVIEW_MD_DIGEST` and `END_TUNING_REVIEW_MD_DIGEST` on their own unindented lines.
- For digest tasks, write command lines as indented plain text inside the digest.

## 4. Task Interfaces

### 4.1 training_run_factual_analysis_task

Skill invocation:
  Use [$training-run-factual-analysis](C:\Users\Dk\Desktop\SCI\Automatic_2\.agents\skills\training-run-factual-analysis\SKILL.md)

Inputs: `source_run_dir`; `run_name`

Outputs: `training_results/current/current_training_run_analysis.json`

Writes: current factual analysis JSON only

Validation: selected_skill_contract; json_parse; required_top_level_keys; factual_summary_status; tuning_recommendation_provided_false; diff_scope; commit_push

### 4.2 single_run_analysis_archive_task

Skill invocation:
  Use [$training-analysis-archive](C:\Users\Dk\Desktop\SCI\Automatic_2\.agents\skills\training-analysis-archive\SKILL.md)

Inputs: current factual JSON; GPT-provided strict `tuning_review_md_digest`; `archive_id`

Outputs: single-run archive factual JSON; single-run archive `tuning_review.md`; `training_results/history/history_index.json`; `training_results/history/tuning_map.md`

Writes: archive pair plus history index plus tuning map

Validation: selected_skill_contract; source_json; tuning_review_md_digest; baseline_fields; compact_history_index_fields; tuning_map_sync; archive_id; diff_scope; commit_push

Digest block rule: Include the supplied digest between `BEGIN_TUNING_REVIEW_MD_DIGEST` and `END_TUNING_REVIEW_MD_DIGEST` markers. Place each marker on its own unindented line.

Digest preservation rule: Preserve the supplied GPT digest as the decision source. Keep digest meaning, `recommendation_type`, `validation_status`, `baseline_update_status`, baseline fields, selected surface, parameter change, recommended run name, command, and validation focus GPT-owned.
