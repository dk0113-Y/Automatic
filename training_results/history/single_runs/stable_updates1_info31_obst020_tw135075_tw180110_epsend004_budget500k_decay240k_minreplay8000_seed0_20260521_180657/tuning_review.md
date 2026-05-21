# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_tw135075_tw180110_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_180657

source_archive_id: stable_updates1_info31_obst020_tw135075_tw180110_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_180657

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst020_tw135075_tw180110_epsend004_budget500k_decay240k_minreplay8000_seed0_20260521_180657

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: launcher-ready angle-specific turn shaping bracket on updated obst020 baseline

parameter_change: RewardTurnWeight135: 0.75 -> 0.6666666666666666; RewardTurnWeight180: 1.10 -> 1.05; keep RewardTurnWeight45=0.0, RewardTurnWeight90=0.3333333333333333, RewardTurnPenaltyScale=0.05, RewardObstacleWeight=0.20, RewardInfoScale=3.1, NStep=3, BatchSize=128, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_tw180105_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior angle-specific turn shaping hypothesis is refuted. Simultaneously increasing RewardTurnWeight135 to 0.75 and RewardTurnWeight180 to 1.10 did not outperform the retained obst020 500k reference baseline. Coverage increased slightly, but success_rate, timeout, episode_length, repeat_visit_ratio, terminal completion, turn burden, and large-angle turn diagnostics remained worse than the reference baseline. The single-seed train-side reference baseline remains obst020. The next bounded run should narrow the angle-specific test by restoring the 135-degree weight to default and applying only a mild 180-degree turn weight increase.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Keeping RewardTurnPenaltyScale=0.05 while increasing RewardTurnWeight135 from 0.6666666666666666 to 0.75 and RewardTurnWeight180 from 1.0 to 1.10 should suppress inefficient large-angle oscillations and revisits, reducing turn_135, turn_180, turn_burden_rate, recent_revisit, stall, episode_length, and timeout while preserving coverage and success_rate.

validation_status: refuted

judgement: refuted. The run did not reduce large-angle turn burden relative to the retained obst020 reference baseline and did not improve completion quality or path efficiency. The increase in angle-specific turn pressure appears to have increased turn penalty burden without reducing inefficient turn frequency.

key_evidence:
- Compared with obst020 500k, the current run reduced reward and success_rate.
- Compared with obst020 500k, the current run increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020 500k, the current run increased turn_ge_90, turn_135, turn_180, and turn_penalty_weight_sum.
- Coverage slightly exceeded the reference baseline, but the gain was insufficient to offset weaker completion and path-efficiency metrics.
- Weighted information gain and obstacle contribution ratio remained close to obst020, so the regression is better explained by local action organization and excessive angle-specific turn burden rather than reward-information composition collapse.

remaining_uncertainty: The combined 135/180 increase is refuted, but the angle-specific surface is not fully closed. The remaining actionable uncertainty is whether only a mild 180-degree penalty increase can suppress reversal-like inefficient turns while preserving necessary 135-degree obstacle/frontier maneuverability.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current angle-specific 135/180 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence suggests the combined 135-degree and 180-degree pressure was too broad. Increasing both weights did not lower large-angle turn counts and instead raised total turn penalty burden while degrading success, timeout, episode length, and repeat visits. A narrower bracket should restore 135-degree default maneuverability and test only a mild 180-degree reversal penalty.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether the current failure was caused by over-penalizing necessary 135-degree maneuvering, and whether a mild 180-degree-only increase can reduce reversal-like inefficient turns without harming coverage, success, or timeout.

next_hypothesis: Restoring RewardTurnWeight135 to the default 0.6666666666666666 while setting RewardTurnWeight180 to 1.05 may preserve 135-degree obstacle/frontier maneuverability and apply only a mild additional penalty to 180-degree reversals. This may reduce turn_180, turn_burden_rate, recent_revisit, stall, episode_length, and timeout while preserving coverage and success_rate. If the run still lowers success_rate or raises timeout relative to obst020, angle-specific turn shaping should be closed and default turn weights retained.

selected_next_surface: launcher-ready angle-specific turn shaping bracket on updated obst020 baseline

parameter_change: RewardTurnWeight135: 0.75 -> 0.6666666666666666; RewardTurnWeight180: 1.10 -> 1.05; keep RewardTurnWeight45=0.0, RewardTurnWeight90=0.3333333333333333, RewardTurnPenaltyScale=0.05, RewardObstacleWeight=0.20, RewardInfoScale=3.1, NStep=3, BatchSize=128, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, TotalEnvSteps=500000, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: The tw135075/tw180110 combination is refuted because it increased large-angle turn burden and did not improve completion or path efficiency. The next test is the narrowest remaining launcher-ready angle-specific bracket: restore 135-degree default behavior and apply a small 180-degree-only penalty increase. This directly tests reversal suppression without continuing to penalize potentially necessary 135-degree maneuvers.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_tw180105_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTurnWeight45 0.0 -RewardTurnWeight90 0.3333333333333333 -RewardTurnWeight135 0.6666666666666666 -RewardTurnWeight180 1.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 500k reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, turn_penalty_weight_sum, turn_burden_rate, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, td_abs_mean, grad_norm, and loss. The run should improve or preserve completion and path-efficiency metrics, not merely lower turn_180.

## 6. Boundaries

reference_baseline_note: The current tw135075/tw180110 run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
