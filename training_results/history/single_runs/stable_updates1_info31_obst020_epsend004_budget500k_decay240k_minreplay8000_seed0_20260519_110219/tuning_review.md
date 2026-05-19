# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

source_archive_id: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

recommendation_type: next_run_plan

prior_validation_status: supported

validation_status: supported

baseline_update_status: updated

current_train_side_reference_baseline_before: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_candidate: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

current_train_side_reference_baseline_after: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function information composition on updated obst020 baseline

parameter_change: RewardObstacleWeight: 0.20 -> 0.15; keep RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

recommended_next_run_name: stable_updates1_info31_obst015_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: The prior obstacle-weight reduction hypothesis is supported. Reducing RewardObstacleWeight from 0.25 to 0.20 while restoring RewardInfoScale=3.1 improved success_rate, timeout, episode_length, repeat_visit_ratio, reward, coverage, and key behavior diagnostics relative to the info31 strong candidate, and also outperformed the retained epsend004 train-side reference baseline on completion and path-efficiency metrics. The current run updates the single-seed train-side reference baseline. The next bounded test should further reduce RewardObstacleWeight from 0.20 to 0.15 to determine whether the information-composition surface still improves completion efficiency or whether 0.20 is the local optimum.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only. posthoc_selection and supplemental_final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Lowering RewardObstacleWeight from 0.25 to 0.20 while restoring RewardInfoScale=3.1 should reduce obstacle-reveal bias inside the information reward, slightly favor empty-space coverage and terminal completion, improve success_rate and timeout, and retain the info31 balance structure.

validation_status: supported

judgement: supported. The current obst020 run improved completion quality and behavior efficiency relative to the info31 strong candidate, while also outperforming the retained epsend004 reference baseline on reward, success_rate, timeout, episode_length, repeat_visit_ratio, and major behavior diagnostics. Coverage was slightly below epsend004 but slightly above info31 and did not block the single-seed train-side baseline update.

key_evidence:
- Compared with info31, obst020 improved reward, coverage, success_rate, episode_length, repeat_visit_ratio, timeout, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, and turn_penalty burden.
- Compared with epsend004, obst020 improved reward, success_rate, episode_length, repeat_visit_ratio, timeout, stall, zero_info, recent_revisit, and turn burden.
- The obstacle_info_contribution_ratio decreased relative to info31, consistent with the intended mechanism of reducing obstacle-reveal bias.
- The only retained caution is that coverage remains slightly below epsend004, so the update is a single-seed train-side reference update, not a global superiority conclusion.

remaining_uncertainty: Whether RewardObstacleWeight=0.20 is the local optimum on this reward-composition surface, or whether a further small reduction to 0.15 can continue improving completion and path efficiency without reducing coverage, reward, or success_rate.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The current obst020 run is admissible and updates the single-seed train-side reference baseline from epsend004 to obst020.

mechanism_summary: The performance pattern supports a reward-composition mechanism rather than a generic reward-pressure mechanism. Restoring RewardInfoScale=3.1 retained information-gain drive, while lowering RewardObstacleWeight to 0.20 reduced obstacle-reveal bias and improved completion efficiency, revisit behavior, stall behavior, and turn burden.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Determine whether RewardObstacleWeight=0.20 is the local optimum, or whether reducing RewardObstacleWeight to 0.15 can further improve completion and path-efficiency metrics without degrading coverage or information-gain behavior.

next_hypothesis: Lowering RewardObstacleWeight from 0.20 to 0.15 may further reduce obstacle-reveal bias and improve timeout, stall, zero_info, repeat_visit_ratio, and turn burden. However, if the obstacle contribution becomes too weak, coverage, weighted_info_gain_sum, or success_rate may regress, indicating that 0.20 is closer to the local optimum.

selected_next_surface: reward-function information composition on updated obst020 baseline

parameter_change: RewardObstacleWeight: 0.20 -> 0.15; keep RewardInfoScale=3.1, RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1, EpsilonEnd=0.04, EpsilonDecaySteps=240000, MinReplaySize=8000

rationale: The current run supports the obstacle-weight reduction direction and updates the train-side baseline. Previously tested terminal bonus, timeout penalty, step penalty, and total information-scale adjustments were refuted or failed to update the baseline. A single-parameter RewardObstacleWeight step from 0.20 to 0.15 keeps the causal boundary narrow and directly tests whether the reward-composition improvement still has headroom.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst015_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.15 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: Compare the next run primarily against the updated obst020 reference baseline. Check success_rate, timeout, coverage, reward, episode_length, repeat_visit_ratio, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, weighted_info_gain_sum, weighted_obstacle_info_gain_sum, obstacle_info_contribution_ratio, td_abs_mean, and grad_norm. The next run should improve or preserve success_rate and timeout while not regressing coverage, reward, RVR, zero_info, recent_revisit, stall, and turn burden toward the info305 or step015 regression pattern.

## 6. Boundaries

reference_baseline_note: The current run updates the single-seed train-side reference baseline to stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219. This update is scoped to train-side single-seed evidence only.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token workflows, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
