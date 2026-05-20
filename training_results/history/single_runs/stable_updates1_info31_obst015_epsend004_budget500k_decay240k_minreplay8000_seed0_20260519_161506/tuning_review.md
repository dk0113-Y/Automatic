# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_161506

source_archive_id: stable_updates1_info31_obst015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_161506

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_candidate: stable_updates1_info31_obst015_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_161506

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: training-budget extension on updated obst020 baseline

parameter_change: TotalEnvSteps: 500000 -> 600000; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, EpsilonDecaySteps=240000, EpsilonEnd=0.04, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst020_budget600k_epsend004_decay240k_minreplay8000_seed0

decision_summary: The prior RewardObstacleWeight=0.15 hypothesis is refuted because the current run regressed versus the updated obst020 reference baseline on reward, coverage, success_rate, timeout, episode length, repeat visit ratio, zero-info, revisit, stall, and turn burden. The baseline remains obst020. A compact candidate-surface scan rejects further obstacle-weight lowering and selects a bounded training-budget extension on the updated obst020 baseline because obst020 retained positive late-stage training dynamics at 500k.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Lowering RewardObstacleWeight from 0.20 to 0.15 should further reduce obstacle-reveal bias and improve timeout, stall, zero_info, repeat_visit_ratio, and turn burden while preserving coverage, reward, and success_rate.

validation_status: refuted

judgement: refuted. The current obst015 run degraded consistently relative to the updated obst020 reference baseline. It reduced obstacle contribution too far and weakened information-use quality rather than improving path efficiency or completion quality.

key_evidence:
- Compared with obst020, obst015 reduced reward, coverage, and success_rate.
- Compared with obst020, obst015 increased timeout, episode_length, and repeat_visit_ratio.
- Compared with obst020, obst015 increased zero_info, recent_revisit, stall, turn_ge_90, turn_135, and turn_180.
- Weighted obstacle information contribution and total weighted information gain decreased, consistent with obstacle-structure information being underweighted rather than optimally debiased.

remaining_uncertainty: RewardObstacleWeight=0.20 is the best observed point in the tested local chain 0.25 -> 0.20 -> 0.15, but this does not prove global optimum or tuning completion. The remaining actionable uncertainty is whether the updated obst020 baseline is still limited by the 500k training budget.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current obst015 run is admissible but does not update the single-seed train-side reference baseline.

mechanism_summary: The evidence supports an obstacle-weight lower-bound interpretation. RewardObstacleWeight=0.20 appears to retain useful obstacle-structure information while reducing obstacle-reveal bias; RewardObstacleWeight=0.15 suppresses obstacle contribution too strongly and causes information-use, completion, and behavior-efficiency regressions.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether the updated obst020 baseline has reached a train-side plateau at 500k, or whether its positive late-stage training dynamics can still translate into improved completion and path efficiency under a moderate budget extension.

next_hypothesis: Extending TotalEnvSteps from 500000 to 600000 while keeping the obst020 reward composition unchanged may improve success_rate, coverage, reward, episode_length, repeat_visit_ratio, timeout, zero_info, and stall by allowing the current best balance point to continue training past a still-positive late-stage trend. If 600k increases reward but worsens timeout, RVR, or episode length, the extension should be treated as late-stage drift and obst020 at 500k should remain the reference baseline.

selected_next_surface: training-budget extension on updated obst020 baseline

parameter_change: TotalEnvSteps: 500000 -> 600000; keep RewardObstacleWeight=0.20, RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, EpsilonDecaySteps=240000, EpsilonEnd=0.04, MinReplaySize=8000

rationale: The candidate-surface scan rejects further RewardObstacleWeight lowering because obst015 is refuted. It also rejects immediate learner/update reopening because the current direct evidence is stronger for budget extension: the updated obst020 baseline showed positive late-stage slopes in reward, coverage, success_rate, episode length, and repeat visit ratio at the 500k endpoint. This is a bounded retest under the updated baseline, not a repetition of the old budget-extension branch under earlier reference settings.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_budget600k_epsend004_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 600000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, obstacle_info_contribution_ratio, td_abs_mean, and grad_norm. The extension should improve or preserve completion and behavior-efficiency metrics rather than only increasing reward.

## 6. Boundaries

reference_baseline_note: The current run does not update the baseline. The single-seed train-side reference baseline remains stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready budget tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
