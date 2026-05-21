# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_224306

source_archive_id: stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_224306

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0_20260520_224306

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: learner/replay credit-assignment on updated obst020 baseline

parameter_change: NStep: 3 -> 4; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardTurnPenaltyScale=0.05, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_nstep4_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior RewardTurnPenaltyScale=0.055 hypothesis is refuted. The turn055 run recovered partially from turn045 but still underperformed the retained obst020 500k reference baseline on reward, coverage, success_rate, timeout, episode_length, repeat_visit_ratio, and key behavior diagnostics. The single-seed train-side reference baseline remains obst020. A compact candidate-surface scan rejects further turn-penalty bracketing and selects a launcher-ready replay/credit-assignment test by increasing NStep from 3 to 4 on the retained obst020 baseline.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Increasing RewardTurnPenaltyScale from 0.05 to 0.055 should suppress inefficient large-angle turning, recent revisits, stall, and zero-info behavior exposed by turn045, thereby improving episode_length, timeout, repeat_visit_ratio, and completion quality while preserving the obst020 reward-information composition.

validation_status: refuted

judgement: refuted. The turn055 run improved relative to turn045 but failed against the actual reference baseline. It did not exceed obst020 500k on completion quality, coverage, reward, path efficiency, or behavior diagnostics.

key_evidence:
- Compared with obst020 500k, turn055 reduced reward, coverage, and success_rate.
- Compared with obst020 500k, turn055 increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, turn055 increased zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, and turn_penalty_weight_sum.
- Turn055 preserved a similar obstacle information contribution ratio, so the remaining weakness is better attributed to local behavior efficiency and credit assignment than obstacle-information composition.

remaining_uncertainty: RewardTurnPenaltyScale=0.055 is refuted. Together with turn045, this suggests RewardTurnPenaltyScale=0.05 is the best observed local balance point. The remaining actionable uncertainty is whether residual timeout, repeat visits, zero-info, and stall are limited by replay/credit assignment rather than reward-coefficient tuning.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current turn055 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence closes the immediate turn-penalty bracket around 0.05. Lowering turn penalty to 0.045 was harmful, and increasing it to 0.055 did not outperform 0.05. Reward-coefficient micro-bracketing is now lower priority than a learner/replay credit-assignment test.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether the retained obst020 baseline's residual timeout, repeat visits, zero-info, and stall can be improved by stronger n-step return propagation rather than additional reward-coefficient tuning.

next_hypothesis: Increasing NStep from 3 to 4 may improve credit assignment for terminal/completion reward and information-gain consequences, helping the policy distinguish productive frontier-entry actions from revisit or stall-prone local actions. This may reduce timeout, episode_length, repeat_visit_ratio, zero_info, and stall while preserving coverage and success_rate. If NStep=4 increases TD or gradient instability or degrades coverage and success, NStep=3 should remain retained.

selected_next_surface: learner/replay credit-assignment on updated obst020 baseline

parameter_change: NStep: 3 -> 4; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardTurnPenaltyScale=0.05, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: RewardObstacleWeight=0.15, TotalEnvSteps=600000, RewardTurnPenaltyScale=0.045, and RewardTurnPenaltyScale=0.055 were all refuted against the retained obst020 baseline. Continuing fine reward-coefficient bracketing has weak evidence. NStep is a single launcher-ready replay/learner parameter with a clear credit-assignment mechanism and a bounded validation target.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_nstep4_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 4 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, grad_norm, and loss. The run should improve or preserve completion and path-efficiency metrics without introducing learner instability.

## 6. Boundaries

reference_baseline_note: The current turn055 run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready replay/learner tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
