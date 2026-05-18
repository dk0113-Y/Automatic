# GPT Tuning Review Digest

## 1. Summary

source_run_name: stable_updates1_info31_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_115820

source_archive_id: stable_updates1_info31_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_115820

recommendation_type: next_run_plan

prior_validation_status: partially_supported

validation_status: partially_supported

baseline_update_status: unchanged

current_train_side_reference_baseline_before: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_candidate: stable_updates1_info31_epsend004_budget500k_decay240k_minreplay8000_seed0_20260518_115820

current_train_side_reference_baseline_after: stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904

baseline_scope: single_seed_train_side_reference_only

selected_next_surface: reward-function step-efficiency / completion balance on top of info31 strong candidate

parameter_change: RewardStepPenalty: 0.02 -> 0.015; keep RewardInfoScale=3.1, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

recommended_next_run_name: stable_updates1_info31_step015_epsend004_budget500k_decay240k_minreplay8000_seed0

decision_summary: RewardInfoScale=3.1 partially supported the information-completion balance hypothesis by improving success_rate, timeout, episode length, RVR, zero_info, stall, and turn burden versus updates1_info32, but epsend004 remains the current reference baseline because info31 still trails epsend004 on coverage, success_rate, timeout, stall, and terminal_bonus_sum.

## 2. Evidence Admission

reproducible_launch_status: reproducible_launch_confirmed

factual_summary_status: factual_summary_ready

admission_result: passed

note: strict_contract_ready supports reproducible launch status but is not a third admission gate. The run is train-side-only, and posthoc/final_probe are skipped_train_side_only_tuning status-only objects with missingness_treatment=not_missing, not performance evidence.

## 3. Prior Validation

prior_hypothesis: Reducing RewardInfoScale from 3.2 to 3.1 while restoring RewardTimeoutPenalty=8 and keeping RewardTerminalBonus=20 and LearnerUpdatesPerIter=1 should improve the information-gain versus terminal-completion trade-off on the LearnerUpdatesPerIter=1 core.

validation_status: partially_supported

judgement: partially_supported. Info31 improved the completion and efficiency trade-off versus updates1_info32 and timeout10, but it did not fully outperform the epsend004 reference baseline on core completion metrics.

key_evidence:
- Info31 improved versus updates1_info32 on success_rate, timeout, episode_length, RVR, zero_info, stall, recent_revisit, and turn burden.
- Info31 improved versus timeout10 on success_rate, timeout, episode_length, RVR, zero_info, recent_revisit, stall, and turn burden.
- Info31 remained weaker than epsend004 on coverage, success_rate, timeout, stall, and terminal_bonus_sum.
- Reward and info_reward_sum decreased versus updates1_info32, which is expected from lowering RewardInfoScale from 3.2 to 3.1.

remaining_uncertainty: Whether a very small step-efficiency / completion adjustment can move success_rate and timeout closer to epsend004 without losing the reward, information-gain, RVR, recent_revisit, zero_info, and turn-burden advantages of info31.

## 4. Current Evidence Digest

train_side_primary: Train-side monitoring is the primary evidence. Info31 is a strong candidate with better completion/efficiency balance than updates1_info32, terminal22, and timeout10, but it does not replace epsend004 as the current reference baseline.

mechanism_summary: RewardInfoScale=3.1 appears to be a better balance point than 3.2 for completion and path efficiency under LearnerUpdatesPerIter=1. The remaining weakness is a small success_rate, timeout, stall, and terminal_bonus gap versus epsend004.

posthoc_context: posthoc_selection was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

final_probe_supplemental: supplemental_final_probe was skipped_train_side_only_tuning with missingness_treatment=not_missing. It is status-only and not evidence.

main_limitation: This is a single-seed train-side reference decision only. It does not establish global optimum, cross-seed robustness, paper-level superiority, method superiority, or tuning completion.

## 5. Next Action

target_uncertainty: Whether a small RewardStepPenalty reduction can improve completion and timeout while preserving info31 reward, coverage, RVR, zero_info, recent_revisit, and turn-burden advantages.

next_hypothesis: Lowering RewardStepPenalty from 0.02 to 0.015 may reduce excessive per-step pressure near terminal completion, improving success_rate, terminal_bonus_sum, and timeout without causing terminal22 or timeout10 style regressions.

selected_next_surface: reward-function step-efficiency / completion balance on top of info31 strong candidate

parameter_change: RewardStepPenalty: 0.02 -> 0.015; keep RewardInfoScale=3.1, RewardTimeoutPenalty=8, RewardTerminalBonus=20, LearnerUpdatesPerIter=1

rationale: TerminalBonus=22 and TimeoutPenalty=10 were refuted, and RewardInfoScale=3.1 is currently the strongest balance candidate. A small RewardStepPenalty reduction is a bounded launcher-ready test aimed at completion hesitation rather than repeating previously refuted terminal/timeout pressure increases.

command: .\scripts\launch_formal_train_stable.ps1 -RunName "stable_updates1_info31_step015_epsend004_budget500k_decay240k_minreplay8000_seed0" -Device "cuda" -Seed 0 -TotalEnvSteps 500000 -EpsilonDecaySteps 240000 -EpsilonEnd 0.04 -MinReplaySize 8000 -FinalGreedyEpisodes 100 -RewardRevisitPenalty 0.10 -RewardTurnPenaltyScale 0.05 -RewardTimeoutPenalty 8 -RewardStepPenalty 0.015 -RewardTerminalBonus 20 -RewardInfoScale 3.1 -RewardObstacleWeight 0.25 -BatchSize 128 -ReplayCapacity 100000 -NStep 3 -Gamma 0.99 -LearningRate 0.0001 -TargetUpdateInterval 1000 -GradClipNorm 10 -CollectStepsPerIter 16 -LearnerUpdatesPerIter 1 -TrainEveryEnvSteps 16 -TrainSideOnlyTuning:$true

expected_validation_focus: compare against retained epsend004 reference baseline and info31 strong candidate; check endpoint success_rate, timeout, terminal_bonus_sum, reward, coverage, episode_length, RVR, zero_info, recent_revisit, stall, turn_ge_90, turn_135, turn_180, info_reward_sum, td_abs_mean, and grad_norm. The run should improve success_rate or timeout relative to info31 without regressing reward, coverage, RVR, zero_info, recent_revisit, and turn burden toward terminal22 or timeout10 patterns.

## 6. Boundaries

reference_baseline_note: epsend004 remains the current single-seed train-side reference baseline. Info31 is a strong candidate and next-run base, not the accepted reference baseline.

evidence_boundary: Train-side monitoring is primary evidence. posthoc/final_probe are skipped status-only objects under train-side-only tuning and cannot establish superiority or evidence insufficiency.

redesign_boundary: This recommendation is launcher-ready reward-coefficient tuning within the current stable method. It does not authorize method-level redesign, old near/mid/token mainline, architecture changes, state representation changes, action-space changes, environment-semantics changes, or paper-level claims.
