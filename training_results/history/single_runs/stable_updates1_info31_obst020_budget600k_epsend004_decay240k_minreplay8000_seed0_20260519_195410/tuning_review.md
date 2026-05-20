# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_budget600k_epsend004_decay240k_minreplay8000_seed0_20260519_195410

source_archive_id: stable_updates1_info31_obst020_budget600k_epsend004_decay240k_minreplay8000_seed0_20260519_195410

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_budget600k_epsend004_decay240k_minreplay8000_seed0_20260519_195410

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function local maneuverability / turn-efficiency balance on updated obst020 baseline

parameter_change: RewardTurnPenaltyScale: 0.05 -> 0.045; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_turn045_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior training-budget extension hypothesis is refuted. Extending the updated obst020 baseline from 500k to 600k produced only a small coverage increase but degraded reward, success_rate, timeout, episode_length, repeat_visit_ratio, zero-info, revisit, stall, and turn-burden diagnostics. The single-seed train-side reference baseline remains the 500k obst020 run. A compact candidate-surface scan rejects further budget extension and selects a launcher-ready local maneuverability test by reducing RewardTurnPenaltyScale from 0.05 to 0.045 on the retained obst020 baseline.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Extending TotalEnvSteps from 500000 to 600000 while keeping the obst020 reward composition unchanged should use the positive late-stage trend observed at 500k to improve success_rate, coverage, reward, episode_length, repeat_visit_ratio, timeout, zero_info, and stall.

validation_status: refuted

judgement: refuted. The 600k run did not convert the prior positive late-stage trend into better endpoint task performance. It slightly improved coverage but degraded completion quality, path efficiency, and behavior diagnostics relative to the updated obst020 500k reference baseline.

key_evidence:
- Compared with obst020 500k, 600k reduced reward and success_rate.
- Compared with obst020 500k, 600k increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, 600k increased zero_info, recent_revisit, stall, turn_ge_90, turn_135, and turn_180.
- The late-stage trend at 600k became drift-like: reward was flat, episode_length and repeat_visit_ratio increased, and stall/zero_info/turn_180 increased.

remaining_uncertainty: Budget extension from 500k to 600k is refuted for the current obst020 configuration. The remaining actionable uncertainty is whether residual stall, zero-info, and turn burden are caused by overly restrictive local turn pressure rather than insufficient training time.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current 600k run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence supports a late-stage drift interpretation rather than a training-budget insufficiency interpretation. The retained obst020 reward composition remains stronger at 500k; the 600k extension mainly worsened path efficiency and local behavior diagnostics.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether the retained obst020 baseline's residual stall, zero-info, timeout, and turn burden can be reduced by slightly relaxing turn-penalty pressure without damaging coverage or completion quality.

next_hypothesis: Lowering RewardTurnPenaltyScale from 0.05 to 0.045 may improve local maneuverability around obstacles and frontier entries, reducing stall, zero-info, episode_length, and timeout while preserving the successful obst020 information-reward composition. If turn counts increase without improvements in success_rate, timeout, episode_length, coverage, or stall, the lower turn penalty should be rejected and RewardTurnPenaltyScale=0.05 should remain retained.

selected_next_surface: reward-function local maneuverability / turn-efficiency balance on updated obst020 baseline

parameter_change: RewardTurnPenaltyScale: 0.05 -> 0.045; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: Further budget extension is refuted by the 600k run, and further RewardObstacleWeight lowering is already refuted by obst015. The 600k failure pattern points to local behavior inefficiency rather than information-reward composition failure. RewardTurnPenaltyScale is a launcher-ready single parameter directly linked to turn burden and episode length, and the proposed small decrease tests whether turn pressure is over-constraining local maneuverability.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_turn045_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.045 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, turn_penalty_weight_sum, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, and grad_norm. The run should improve or preserve completion and path-efficiency metrics, not merely increase turn freedom.

## 6. Boundaries

reference_baseline_note: The current 600k run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
