# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_bs192_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_142144

source_archive_id: stable_updates1_info31_obst020_bs192_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_142144

recommendation_type: next_run_plan

prior_validation_status: partially_supported

validation_status: partially_supported

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_bs192_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_142144

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: launcher-ready angle-specific turn shaping on updated obst020 baseline

parameter_change: RewardTurnWeight135: 0.6666666666666666 -> 0.75; RewardTurnWeight180: 1.0 -> 1.10; keep RewardTurnWeight45=0.0, RewardTurnWeight90=0.3333333333333333, RewardTurnPenaltyScale=0.05, RewardObstacleWeight=0.20, RewardInfoScale=3.1, NStep=3, BatchSize=128, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_tw135075_tw180110_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior BatchSize=192 learner-stability hypothesis is only partially supported. The run reduced learner-noise indicators such as loss and td_abs_mean, but failed the performance-improvement objective because reward, coverage, success_rate, timeout, episode_length, repeat_visit_ratio, zero-info, recent revisit, stall, and turn burden remained worse than the retained obst020 500k reference baseline. The single-seed train-side reference baseline remains obst020. Since scalar reward coefficients, budget extension, n-step horizon, and batch-size learner stability have not updated the baseline, and since Codex has now exposed angle-specific turn weights as launcher-ready parameters, the next bounded run should test higher 135/180 turn weights while preserving 45/90 maneuverability.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Increasing BatchSize from 128 to 192 should reduce gradient noise and TD-estimation fluctuation, making Q updates more stable under the retained obst020 reward composition and NStep=3. This should reduce timeout, episode_length, repeat_visit_ratio, zero_info, and stall while preserving coverage and success_rate.

validation_status: partially_supported

judgement: partially_supported. The learner-stability sub-hypothesis is supported because loss, td_abs_mean, and grad_norm improved or stayed controlled relative to obst020. The performance-improvement hypothesis is refuted because the endpoint task metrics and behavior diagnostics degraded relative to the retained obst020 500k reference baseline. Therefore the run does not update the baseline.

key_evidence:
- Compared with obst020 500k, bs192 reduced loss and td_abs_mean, with grad_norm slightly lower, supporting the learner-noise reduction sub-objective.
- Compared with obst020 500k, bs192 reduced reward, coverage, and success_rate.
- Compared with obst020 500k, bs192 increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, bs192 increased zero_info, recent_revisit, stall, turn burden, turn_135, and turn_180.
- Weighted information gain and obstacle contribution ratio remained close to obst020, so the main regression is better explained by local behavior organization and update responsiveness rather than reward-information composition collapse.

remaining_uncertainty: BatchSize=192 does not solve residual timeout, revisit, zero-info, stall, or turn burden. The remaining actionable uncertainty is whether angle-specific turn shaping can suppress inefficient 135/180 turns while preserving useful 45/90 local maneuverability.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current bs192 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence shows that smoother learner metrics do not translate into better exploration behavior for this baseline. The persistent residuals are local behavior diagnostics: turn burden, 135/180 turns, recent revisit, stall, episode length, and timeout. Scalar RewardTurnPenaltyScale bracketing failed in both directions, but angle-specific turn weights are now launcher-ready after Codex exposure. This enables a narrower mechanism test: increase large-angle turn penalties while preserving smaller-angle maneuverability.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether persistent turn burden, recent revisit, stall, episode_length, and timeout can be reduced by selectively increasing large-angle turn penalties without over-constraining necessary small-angle obstacle/frontier maneuvering.

next_hypothesis: Keeping RewardTurnPenaltyScale=0.05 while increasing RewardTurnWeight135 from 0.6666666666666666 to 0.75 and RewardTurnWeight180 from 1.0 to 1.10 may suppress inefficient large-angle oscillations and revisits. This should reduce turn_135, turn_180, turn_burden_rate, recent_revisit, stall, episode_length, and timeout while preserving coverage and success_rate. If large-angle turns decrease but coverage, success_rate, timeout, or episode_length degrade, the angle-specific pressure is too strong and default turn weights should be retained.

selected_next_surface: launcher-ready angle-specific turn shaping on updated obst020 baseline

parameter_change: RewardTurnWeight135: 0.6666666666666666 -> 0.75; RewardTurnWeight180: 1.0 -> 1.10; keep RewardTurnWeight45=0.0, RewardTurnWeight90=0.3333333333333333, RewardTurnPenaltyScale=0.05, RewardObstacleWeight=0.20, RewardInfoScale=3.1, NStep=3, BatchSize=128, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: RewardObstacleWeight=0.15, TotalEnvSteps=600000, RewardTurnPenaltyScale=0.045, RewardTurnPenaltyScale=0.055, NStep=4, and BatchSize=192 did not update the retained obst020 baseline. The remaining consistent weakness is local behavior efficiency, especially turn burden and revisit/stall behavior. Angle-specific turn shaping is now launcher-ready and allows a more precise test than scalar turn penalty scaling by targeting 135/180 turns while preserving 45/90 maneuverability.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_tw135075_tw180110_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTurnWeight45 0.0 -RewardTurnWeight90 0.3333333333333333 -RewardTurnWeight135 0.75 -RewardTurnWeight180 1.10 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, turn_penalty_weight_sum, turn_burden_rate, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, grad_norm, and loss. The run should improve or preserve completion and path-efficiency metrics, not merely reduce large-angle turns.

## 6. Boundaries

reference_baseline_note: The current bs192 run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method after Codex exposed existing angle-specific turn-weight fields. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
