# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_step015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_154129

source_archive_id: stable_updates1_info31_step015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_154129

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_candidate: stable_updates1_info31_step015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_154129

current_train_side_reference_baseline_after: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function information-completion balance on top of LearnerUpdatesPerIter=1

parameter_change: RewardInfoScale: 3.1 -> 3.05; restore RewardStepPenalty=0.02; keep RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

recommended_next_run_name: stable_updates1_info305_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: RewardStepPenalty=0.015 was refuted because it reduced step penalty cost but worsened reward, coverage, success_rate, timeout, episode length, RVR, zero_info, stall, and turn burden; epsend004 remains the reference baseline and the next bounded test should restore RewardStepPenalty=0.02 while lowering RewardInfoScale from 3.1 to 3.05.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only, and posthoc/final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Lowering RewardStepPenalty from 0.02 to 0.015 on top of the info31 strong candidate should reduce excessive per-step pressure near terminal completion, improving success_rate, terminal_bonus_sum, and timeout without causing terminal22 or timeout10 style regressions.

validation_status: refuted

judgement: refuted. Step015 reduced step_penalty_sum and improved some learner scalars, but it significantly worsened the task-level completion and exploration-efficiency metrics versus info31 and did not outperform epsend004.

key_evidence:
- Step015 regressed versus info31 on reward, coverage, success_rate, episode_length, RVR, timeout, zero_info, recent_revisit, stall, and turn burden.
- Step015 remained weaker than epsend004 on coverage, success_rate, episode_length, RVR, timeout, zero_info, and stall, despite slightly higher reward.
- Step_penalty_sum became less negative, but terminal_bonus_sum fell and timeout_penalty_sum worsened, so the reward-component improvement did not translate into better completion.
- Learner scalars improved locally, but learner diagnostics are not standalone superiority evidence.

remaining_uncertainty: Whether a smaller information-gain reduction from RewardInfoScale=3.1 to 3.05 can improve success_rate and timeout without recreating the step015 path-efficiency collapse or losing the info31 balance advantages.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. Step015 is a valid run but an invalid tuning direction because it worsened both completion quality and exploration efficiency relative to info31.

mechanism_summary: Lowering RewardStepPenalty likely weakened the pressure for efficient completion, increasing episode length, repeat visiting, zero-info steps, stall, and timeout. The evidence points away from step-penalty reduction and back toward fine-grained RewardInfoScale interpolation.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Whether RewardInfoScale=3.05 can preserve info31 reward, coverage, RVR, zero_info, recent_revisit, and turn-burden advantages while moving success_rate and timeout closer to epsend004.

next_hypothesis: Lowering RewardInfoScale from 3.1 to 3.05 while restoring RewardStepPenalty=0.02 may further reduce the information-gain versus terminal-completion trade-off, improving success_rate and timeout without causing the step015 regression pattern.

selected_next_surface: reward-function information-completion balance on top of LearnerUpdatesPerIter=1

parameter_change: RewardInfoScale: 3.1 -> 3.05; restore RewardStepPenalty=0.02; keep RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

rationale: TerminalBonus=22, TimeoutPenalty=10, and StepPenalty=0.015 were refuted. RewardInfoScale=3.1 remains the strongest balance candidate, but it still trails epsend004 on completion metrics. A small RewardInfoScale interpolation to 3.05 is a bounded launcher-ready test that targets the remaining information-completion trade-off without repeating refuted terminal, timeout, or step-penalty directions.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info305_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.05 -RewardObstacleWeight 0.25 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: compare against retained epsend004 reference baseline and info31 strong candidate; check endpoint success_rate, timeout, terminal_bonus_sum, reward, coverage, episode_length, RVR, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, td_abs_mean, and grad_norm. The run should improve success_rate or timeout relative to info31 without regressing reward, coverage, RVR, zero_info, recent_revisit, and turn burden toward the step015 pattern.

## 6. Boundaries

reference_baseline_note: epsend004 remains the current single-seed train-side reference baseline. Info31 remains a strong candidate and mechanism base, while step015 does not update the reference baseline.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token mainline, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
