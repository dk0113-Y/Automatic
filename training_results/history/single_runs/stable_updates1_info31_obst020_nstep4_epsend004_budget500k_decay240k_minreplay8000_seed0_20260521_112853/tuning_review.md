# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_nstep4_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_112853

source_archive_id: stable_updates1_info31_obst020_nstep4_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_112853

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_nstep4_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_112853

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: learner/update stability on updated obst020 baseline

parameter_change: BatchSize: 128 -> 192; restore NStep=3; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardTurnPenaltyScale=0.05, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_bs192_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior NStep=4 credit-assignment hypothesis is refuted. The nstep4 run degraded reward, coverage, success_rate, timeout, episode_length, repeat_visit_ratio, zero-info, revisit, stall, turn burden, and learner stability relative to the retained obst020 500k reference baseline. The single-seed train-side reference baseline remains obst020. A compact candidate-surface scan rejects further n-step horizon increase and selects a launcher-ready batch-level learner stability test by increasing BatchSize from 128 to 192 while restoring NStep=3.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Increasing NStep from 3 to 4 should improve credit assignment for terminal/completion reward and information-gain consequences, helping the policy distinguish productive frontier-entry actions from revisit or stall-prone local actions. It should reduce timeout, episode_length, repeat_visit_ratio, zero_info, and stall while preserving coverage and success_rate.

validation_status: refuted

judgement: refuted. The nstep4 run did not improve credit assignment or behavior efficiency. It degraded completion quality, coverage, reward, path efficiency, behavior diagnostics, and learner stability relative to the obst020 500k reference baseline.

key_evidence:
- Compared with obst020 500k, nstep4 reduced reward, coverage, and success_rate.
- Compared with obst020 500k, nstep4 increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, nstep4 increased zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, and turn_penalty_weight_sum.
- nstep4 increased td_abs_mean, grad_norm, and loss relative to obst020, suggesting higher target variance or value-estimation noise rather than improved credit assignment.
- Obstacle information contribution remained close to obst020, so the regression is better explained by learner/replay horizon instability and path-organization degradation rather than reward-information composition failure.

remaining_uncertainty: NStep=4 is refuted, and current evidence does not support further increasing n-step horizon. The remaining actionable uncertainty is whether residual timeout, repeat visits, zero-info, and stall are limited by batch-level update stability rather than reward-coefficient tuning or longer return propagation.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current nstep4 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence supports an n-step overextension interpretation. Longer return propagation increased learner instability and did not improve completion or path efficiency. NStep=3 remains the retained setting under the obst020 baseline. Since reward-coefficient brackets, budget extension, turn-penalty brackets, and n-step extension have all failed to update the baseline, the next bounded learner surface should test batch-level update stability.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether the retained obst020 baseline's residual timeout, repeat visits, zero-info, and stall can be improved by more stable batch-level learner updates rather than longer n-step return propagation or additional reward-coefficient micro-bracketing.

next_hypothesis: Increasing BatchSize from 128 to 192 may reduce gradient noise and TD-estimation fluctuation, making Q updates more stable under the retained obst020 reward composition and NStep=3. This may reduce timeout, episode_length, repeat_visit_ratio, zero_info, and stall while preserving coverage and success_rate. If BatchSize=192 reduces learner noise but worsens coverage, success_rate, or path efficiency, BatchSize=128 should remain retained.

selected_next_surface: learner/update stability on updated obst020 baseline

parameter_change: BatchSize: 128 -> 192; restore NStep=3; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardTurnPenaltyScale=0.05, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: NStep=4 is refuted and showed worse learner stability. RewardObstacleWeight=0.15, TotalEnvSteps=600000, RewardTurnPenaltyScale=0.045, and RewardTurnPenaltyScale=0.055 were also refuted against the retained obst020 baseline. A BatchSize 128 to 192 test is a single launcher-ready learner-stability intervention. It is more conservative than the historically tested BatchSize=256 and avoids directly repeating a higher-risk batch-size setting that did not update the earlier baseline.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_bs192_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 192 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, grad_norm, and loss. The run should improve or preserve completion and path-efficiency metrics while reducing or at least not worsening learner instability relative to the retained baseline.

## 6. Boundaries

reference_baseline_note: The current nstep4 run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready replay/learner tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
