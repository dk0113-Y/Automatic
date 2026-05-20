# Tuning Map

## 1. Scope

- Compact navigation summary for GPT tuning review.
- Source: `training_results/history/history_index.json` and archived GPT tuning reviews.
- Not decision authority; GPT decides from current factual report, matched prior review, this map, and system manifest.
- No raw metrics, full commands, full digests, global optimum, paper-level, cross-seed, or tuning-completion claims.
- Preserve archived GPT meanings; use `unresolved` or `not_recorded` when a conclusion is not explicit.

## 2. Current Train-Side Reference Baseline

| Field | Value |
| --- | --- |
| current_train_side_reference_baseline | `stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0_20260519_110219` |
| basis | Archived GPT review supported obst020 and updated the single-seed train-side reference baseline from epsend004; obst015, obst020_600k, and turn045 reviews retained obst020. |
| scope | `single_seed_train_side_reference_only` |
| caveat | Not a global optimum, tuning-completion claim, paper-level conclusion, or cross-seed conclusion. |

## 3. Active Tuning Branch

| Field | Value |
| --- | --- |
| branch objective | Single-seed train-side performance search under formal stable settings. |
| primary evidence | Train-side monitoring in the current factual report. |
| context evidence | posthoc/final_probe only when GPT admits them as status or supplemental context. |
| retained core reference settings | epsend004 core: 500k budget, decay 240k, epsilon_end 0.04, min replay 8000, seed 0. |
| current mechanism base | updates1/info31/obst020 is the updated reference baseline for the active reward-composition branch. |

## 4. Tuning Path Summary

| Archive label | Validation | Baseline outcome | Next direction |
| --- | --- | --- | --- |
| baseline 20260508 | `not_applicable` | Initial paired record. | Budget extension. |
| extend650k | `refuted` | 500k epsilon_end=0.05 retained. | Lower epsilon_end to 0.03. |
| epsend003 | `refuted` | 500k epsilon_end=0.05 retained. | EpsilonEnd 0.04. |
| epsend004 | `supported` | Became current reference baseline. | EpsilonEnd 0.035. |
| epsend0035 | `refuted` | No baseline update. | Longer epsilon decay. |
| epsdecay300k | `refuted` | No baseline update. | Revisit penalty 0.12. |
| revisit012 | `refuted` | No baseline update. | Turn penalty 0.06. |
| turn006 | `refuted` | No baseline update. | Learning rate 0.000075. |
| lr75e5 | `refuted` | No baseline update. | Batch size 256. |
| bs256 | `partially_supported` | No baseline update. | Bounded learner/update comparison. |
| learner/update comparison | `partially_supported` | No baseline update. | RewardInfoScale 3.2 on updates1. |
| updates1_info32 | `partially_supported` | No baseline update; info32 strong candidate. | Terminal bonus 22. |
| terminal22 | `refuted` | No baseline update. | Timeout penalty 10. |
| timeout10 | `refuted` | No baseline update. | RewardInfoScale 3.1. |
| updates1_info31 | `partially_supported` | No baseline update; info31 strong candidate/base. | Step penalty 0.015. |
| step015 | `refuted` | No baseline update. | RewardInfoScale 3.05. |
| info305 | `refuted` | No baseline update. | Obstacle weight 0.20 on info31. |
| obst020 | `supported` | Baseline updated to obst020. | Obstacle weight 0.15 on obst020. |
| obst015 | `refuted` | Baseline remains obst020. | Budget extension on obst020. |
| obst020_600k | `refuted` | Baseline remains obst020 500k. | Turn penalty 0.045 on obst020. |
| turn045 | `refuted` | Baseline remains obst020 500k. | Latest active: turn penalty 0.055 on obst020. |

## 5. Refuted Directions

| Direction group | Archived GPT conclusion |
| --- | --- |
| Budget extension and epsilon schedule search | 650k, obst020 600k, epsilon_end 0.03, epsilon_end 0.035, and decay 300k were refuted or did not update baseline. |
| Revisit/turn shaping increases | RewardRevisitPenalty 0.12 and RewardTurnPenaltyScale 0.06 were refuted under epsend004 core. |
| Learner learning-rate reduction | LearningRate 0.000075 was refuted under epsend004 core. |
| Terminal/timeout pressure on info32 | RewardTerminalBonus 22 and RewardTimeoutPenalty 10 were refuted on updates1_info32. |
| Step-efficiency pressure on info31 | RewardStepPenalty 0.015 was refuted on info31. |
| Total info-scale reduction from info31 | RewardInfoScale 3.05 was refuted and info305 did not update baseline. |
| Obstacle-weight lowering below obst020 | RewardObstacleWeight 0.15 was refuted on the updated obst020 baseline. |
| Turn-penalty lowering on obst020 | RewardTurnPenaltyScale 0.045 was refuted on the updated obst020 baseline. |

## 6. Supported / Retained Directions

| Direction | Archived GPT conclusion |
| --- | --- |
| epsend004 retained as train-side reference across refuted/partial branches before obst020 | Retained through exploration, reward-shaping, learner/update, info32, info31, step015, and info305 branches. |
| EpsilonEnd 0.04 at 500k decay 240k min replay 8000 | Supported by epsend004 and retained in the updated obst020 reference baseline. |
| LearnerUpdatesPerIter 1 branch | Bounded learner/update comparison made updates1 the strongest mechanism candidate but did not update baseline. |
| updates1_info32 | Strong candidate context; did not update reference baseline. |
| updates1_info31 | Strong candidate and current mechanism base; did not update reference baseline. |
| RewardObstacleWeight 0.20 on info31 | Supported by obst020, updated the single-seed train-side reference baseline, and was retained after obst015, obst020_600k, and turn045. |

## 7. Open Uncertainties

| Uncertainty | Navigation status |
| --- | --- |
| Local maneuverability on obst020 | Active: test whether RewardTurnPenaltyScale 0.05 is the local balance point or whether a small increase to 0.055 can suppress inefficient turning and revisits without over-constraining obstacle/frontier maneuvering. |
| Reward pressure trade-off | Terminal, timeout, step-penalty, and total info-scale moves were refuted; use only GPT-explicit bounded alternatives. |
| Learner/update dynamics | Candidate context remains useful if reward-composition tuning stalls; no active next surface unless GPT reopens it. |
| Posthoc/final_probe mismatch | Context only; not baseline decision authority. |
| Cross-seed robustness | Not evaluated by this single-seed map and not the default blocker. |

## 8. Next Candidate Surfaces

| Surface | Status from archived GPT review |
| --- | --- |
| `stable_updates1_info31_obst020_turn055_epsend004_budget500k_decay240k_minreplay8000_seed0` | Latest active candidate from turn045 review. Parameter change: `RewardTurnPenaltyScale: 0.05 -> 0.055`; keep `RewardObstacleWeight=0.20`, `RewardInfoScale=3.1`, step penalty 0.02, timeout penalty 8, terminal bonus 20, updates per iter 1, 500k budget, epsilon_end 0.04, decay 240k, and min replay 8000. Context: obst020 remains the updated single-seed train-side reference baseline. |

## 9. Maintenance Rule

- Update only from `history_index.json` entries and archived GPT tuning reviews.
- Keep current navigation, not repeated chronological prose.
- Aggregate retained-baseline outcomes once; do not append one row per archive.
- Remove or demote next surfaces after they are executed, refuted, or superseded unless GPT marks them pending/conditional.
- Do not duplicate full archived reviews, raw outputs, logs, CSVs, plots, trajectories, checkpoints, model weights, or commands.
