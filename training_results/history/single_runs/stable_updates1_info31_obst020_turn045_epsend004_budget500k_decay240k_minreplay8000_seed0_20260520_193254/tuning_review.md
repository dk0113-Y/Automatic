# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_turn045_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_193254

source_archive_id: stable_updates1_info31_obst020_turn045_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_193254

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_turn045_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_193254

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function local maneuverability / turn-efficiency bracket on updated obst020 baseline

parameter_change: RewardTurnPenaltyScale: 0.05 -> 0.055; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior RewardTurnPenaltyScale=0.045 hypothesis is refuted. Lowering turn penalty degraded reward, coverage, success_rate, timeout, episode_length, repeat_visit_ratio, zero-info, revisit, stall, and turn-burden diagnostics relative to the retained obst020 500k reference baseline. The single-seed train-side reference baseline remains obst020. A compact candidate-surface scan selects a small reverse bracket test by increasing RewardTurnPenaltyScale from 0.05 to 0.055 on the retained obst020 baseline.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Lowering RewardTurnPenaltyScale from 0.05 to 0.045 should improve local maneuverability around obstacles and frontier entries, reducing stall, zero-info, episode_length, and timeout while preserving the successful obst020 information-reward composition.

validation_status: refuted

judgement: refuted. The turn045 run did not improve local maneuverability or completion quality. It degraded task completion, path efficiency, revisit behavior, stall, zero-info, and turn burden relative to the obst020 500k reference baseline.

key_evidence:
- Compared with obst020 500k, turn045 reduced reward, coverage, and success_rate.
- Compared with obst020 500k, turn045 increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, turn045 increased zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, and turn_penalty_weight_sum.
- Obstacle information contribution remained close to obst020, so the regression is better explained by degraded local action organization rather than reward-information composition failure.

remaining_uncertainty: RewardTurnPenaltyScale=0.045 is refuted. The remaining actionable uncertainty is whether RewardTurnPenaltyScale=0.05 is already the local balance point, or whether a small increase to 0.055 can suppress inefficient turning and revisits without over-constraining obstacle/frontier maneuvering.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current turn045 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence supports a turn-penalty lower-bound interpretation. Reducing the turn penalty weakened control over inefficient local motion, increasing turn burden, revisit behavior, stall, zero-info, timeout, and completion degradation. The retained obst020 reward-information composition remains stronger.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether RewardTurnPenaltyScale=0.05 is already the local balance point or whether a small increase to 0.055 can reduce inefficient turning, revisit behavior, stall, zero-info, timeout, and episode length without reducing coverage or success_rate.

next_hypothesis: Increasing RewardTurnPenaltyScale from 0.05 to 0.055 may suppress the inefficient large-angle turning, recent revisits, stall, and zero-info behavior exposed by turn045, thereby improving episode_length, timeout, repeat_visit_ratio, and completion quality while keeping the obst020 reward-information composition unchanged. If 0.055 reduces turning but worsens coverage, success_rate, timeout, or episode length, then RewardTurnPenaltyScale=0.05 should be retained as the local balance point.

selected_next_surface: reward-function local maneuverability / turn-efficiency bracket on updated obst020 baseline

parameter_change: RewardTurnPenaltyScale: 0.05 -> 0.055; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: RewardTurnPenaltyScale=0.045 is refuted by worse completion and behavior efficiency. Budget extension and further obstacle-weight lowering are also refuted. A small reverse turn-penalty bracket is the narrowest launcher-ready test still directly tied to the current failure pattern. It is not a repeat of the old RewardTurnPenaltyScale=0.06 test because the baseline and mechanism context have changed, and the proposed change is smaller.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.055 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline, not against turn045. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, turn_penalty_weight_sum, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, and grad_norm. The run should improve or preserve completion and path-efficiency metrics rather than merely increasing turn cost.

## 6. Boundaries

reference_baseline_note: The current turn045 run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
