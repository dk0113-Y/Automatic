# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info32_timeout10_epsend004_budget500k_decay240k_minreplay8000_seed0_20260517_201806

source_archive_id: stable_updates1_info32_timeout10_epsend004_budget500k_decay240k_minreplay8000_seed0_20260517_201806

recommendation_type: next_run_plan

prior_validation_status: refuted

validation_status: refuted

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_candidate: stable_updates1_info32_timeout10_epsend004_budget500k_decay240k_minreplay8000_seed0_20260517_201806

current_train_side_reference_baseline_after: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function information-completion balance on top of LearnerUpdatesPerIter=1

parameter_change: RewardInfoScale: 3.2 -> 3.1; restore RewardTimeoutPenalty=8; keep RewardTerminalBonus=20 and LearnerUpdatesPerIter=1

recommended_next_run_name: stable_updates1_info31_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: RewardTimeoutPenalty=10 repaired part of terminal22 but did not improve over updates1_info32 or update the epsend004 reference baseline; the next bounded test should restore RewardTimeoutPenalty=8 and reduce RewardInfoScale from 3.2 to 3.1 on the LearnerUpdatesPerIter=1 core.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only, and posthoc/final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Restoring RewardTerminalBonus=20 and increasing RewardTimeoutPenalty from 8 to 10 on top of the updates1_info32 strong-candidate core should penalize timeout failure more directly, improving timeout and success_rate while preserving RewardInfoScale=3.2 and LearnerUpdatesPerIter=1 benefits.

validation_status: refuted

judgement: refuted. Timeout10 improved over the already-refuted terminal22 run, but it did not improve over updates1_info32 on the intended completion and efficiency targets and did not outperform epsend004 on core completion metrics.

key_evidence:
- timeout10 improved versus terminal22 in reward, coverage, success_rate, timeout, RVR, recent_revisit, and stall, showing partial repair of the terminal22 regression.
- timeout10 remained weaker than updates1_info32 on reward, success_rate, episode_length, RVR, timeout, recent_revisit, stall, turn burden, and learner diagnostics.
- timeout10 remained weaker than epsend004 on coverage, success_rate, timeout, zero_info, and stall, despite higher reward and lower recent_revisit / turn burden.
- Increasing timeout penalty to 10 made timeout_penalty_sum more negative but did not reduce timeout below updates1_info32.

remaining_uncertainty: Whether reducing RewardInfoScale from 3.2 to 3.1 while restoring RewardTimeoutPenalty=8 can improve the information-gain versus terminal-completion trade-off on the LearnerUpdatesPerIter=1 core.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. The run is valid and learned over training, but timeout10 did not validate the intended timeout-completion improvement over updates1_info32 and did not qualify as a reference baseline update.

mechanism_summary: Timeout10 suggests that direct timeout penalty scaling partially repairs terminal22 but does not solve the completion trade-off. The evidence points away from further terminal/timeout reward escalation and toward rebalancing RewardInfoScale between the Candidate B / updates1 behavior and the higher-info updates1_info32 behavior.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Whether RewardInfoScale=3.1 can preserve much of the reward, information-gain, and path-efficiency benefit of updates1_info32 while reducing the success_rate and timeout deficit seen at RewardInfoScale=3.2.

next_hypothesis: Lowering RewardInfoScale from 3.2 to 3.1 while keeping LearnerUpdatesPerIter=1 and restoring RewardTimeoutPenalty=8 may produce a better information-completion balance, improving success_rate and timeout without sacrificing reward, coverage, RVR, recent_revisit, and turn burden to the terminal22 or timeout10 regression pattern.

selected_next_surface: reward-function information-completion balance on top of LearnerUpdatesPerIter=1

parameter_change: RewardInfoScale: 3.2 -> 3.1; restore RewardTimeoutPenalty=8; keep RewardTerminalBonus=20 and LearnerUpdatesPerIter=1

rationale: Terminal22 and timeout10 show that increasing terminal-completion-side reward pressure does not fix the remaining success_rate and timeout deficit. The strongest remaining evidence points to RewardInfoScale as the likely information-completion trade-off knob, because RewardInfoScale=3.2 improved reward and information gain but left success_rate and timeout weaker than epsend004, while RewardInfoScale=3.0 under LearnerUpdatesPerIter=1 had stronger completion behavior but weaker information drive.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.02 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.25 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: compare against retained epsend004 reference baseline and updates1_info32 strong candidate; check endpoint reward, coverage, success_rate, timeout, episode_length, RVR, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, terminal_bonus_sum, timeout_penalty_sum, td_abs_mean, and grad_norm. The run should improve success_rate or timeout relative to updates1_info32 without losing the reward, coverage, information-gain, RVR, recent_revisit, and turn-burden advantages into terminal22 or timeout10 regression.

## 6. Boundaries

reference_baseline_note: epsend004 remains the current single-seed train-side reference baseline. updates1_info32 remains a strong candidate and mechanism base, while timeout10 does not update the reference baseline.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token mainline, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
