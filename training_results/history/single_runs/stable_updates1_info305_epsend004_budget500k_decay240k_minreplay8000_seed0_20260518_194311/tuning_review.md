# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info305_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_194311

source_archive_id: stable_updates1_info305_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_194311

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_candidate: stable_updates1_info305_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_194311

current_train_side_reference_baseline_after: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function information composition on top of info31 strong candidate

parameter_change: RewardObstacleWeight: 0.25 -> 0.20; restore RewardInfoScale=3.1; keep RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

recommended_next_run_name: stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: RewardInfoScale=3.05 was refuted because it repaired step015 but failed to improve over info31 or update the epsend004 reference baseline; the next bounded test should restore RewardInfoScale=3.1 and reduce RewardObstacleWeight from 0.25 to 0.20.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only, and posthoc/final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Lowering RewardInfoScale from 3.1 to 3.05 while restoring RewardStepPenalty=0.02 should further reduce the information-gain versus terminal-completion trade-off, improving success_rate and timeout without causing the step015 regression pattern.

validation_status: refuted

judgement: refuted. Info305 repaired the already-refuted step015 regression, but it did not improve over the stronger info31 candidate and did not outperform epsend004 on core completion metrics.

key_evidence:
- Info305 improved over step015 on reward, coverage, success_rate, episode_length, RVR, timeout, zero_info, and stall.
- Info305 regressed versus info31 on reward, coverage, success_rate, timeout, RVR, recent_revisit, turn burden, and terminal_bonus_sum.
- Info305 remained weaker than epsend004 on coverage, success_rate, timeout, stall, and terminal_bonus_sum.
- Lowering total RewardInfoScale from 3.1 to 3.05 did not produce the intended completion improvement.

remaining_uncertainty: Whether adjusting information reward composition through RewardObstacleWeight can improve completion quality without continuing refuted total RewardInfoScale interpolation or terminal/timeout/step penalty directions.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. Info305 is a valid run but an invalid improvement direction because it does not improve over info31 and does not update the epsend004 reference baseline.

mechanism_summary: RewardInfoScale=3.05 appears to weaken information-gain drive without improving completion quality. The evidence points back to RewardInfoScale=3.1 and toward a finer information-composition adjustment rather than further total info-scale reduction.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Whether reducing RewardObstacleWeight from 0.25 to 0.20 while restoring RewardInfoScale=3.1 can improve success_rate and timeout by shifting information reward composition away from obstacle reveal contribution without losing info31 reward, coverage, and path-efficiency advantages.

next_hypothesis: Lowering RewardObstacleWeight from 0.25 to 0.20 may reduce obstacle-reveal bias inside the information reward and slightly favor empty-space coverage and terminal completion, improving success_rate and timeout while retaining the info31 balance structure.

selected_next_surface: reward-function information composition on top of info31 strong candidate

parameter_change: RewardObstacleWeight: 0.25 -> 0.20; restore RewardInfoScale=3.1; keep RewardStepPenalty=0.02, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

rationale: TerminalBonus=22, TimeoutPenalty=10, StepPenalty=0.015, and RewardInfoScale=3.05 were refuted. RewardInfoScale=3.1 remains the strongest balance candidate, and RewardObstacleWeight is a launcher-ready reward coefficient that has not yet been refuted in this chain. This is a bounded test of information-reward composition rather than another total reward-pressure change.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.20 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: compare against retained epsend004 reference baseline and info31 strong candidate; check endpoint success_rate, timeout, terminal_bonus_sum, coverage, reward, episode_length, RVR, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, weighted_obstacle_info_gain_sum, obstacle_info_contribution_ratio, td_abs_mean, and grad_norm. The run should improve success_rate or timeout relative to info31 without regressing reward, coverage, RVR, zero_info, recent_revisit, and turn burden toward the info305 or step015 pattern.

## 6. Boundaries

reference_baseline_note: epsend004 remains the current single-seed train-side reference baseline. Info31 remains a strong candidate and mechanism base, while info305 does not update the reference baseline.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token mainline, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
