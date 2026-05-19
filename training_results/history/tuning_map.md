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
| current_train_side_reference_baseline | `stable_epsend004_budget500k_decay240k_minreplay8000_seed0_20260511_195904` |
| basis | Archived GPT review supported epsend004; later refuted/partial branches retained epsend004 without baseline update. |
| scope | `single_seed_train_side_reference_only` |
| caveat | Not a global optimum, tuning-completion claim, paper-level conclusion, or cross-seed conclusion. |

## 3. Active Tuning Branch

| Field | Value |
| --- | --- |
| branch objective | Single-seed train-side performance search under formal stable settings. |
| primary evidence | Train-side monitoring in the current factual report. |
| context evidence | posthoc/final_probe only when GPT admits them as status or supplemental context. |
| retained core reference settings | epsend004 core: 500k budget, decay 240k, epsilon_end 0.04, min replay 8000, seed 0. |
| current mechanism base | updates1/info31 is a strong candidate base; epsend004 remains the reference baseline. |

## 4. Tuning Path Summary

| Archive label | Validation | Baseline outcome | Next direction |
| --- | --- | --- | --- |
| baseline 20260508 | `not_applicable` | Initial paired record. | Budget extension. |
| extend650k | `refuted` | 500k epsilon_end=0.05 retained. | Lower epsilon_end to 0.03. |
| epsend003 | `refuted` | 500k epsilon_end=0.05 retained. | EpsilonEnd 0.04. |
| epsend004 | `supported` | Became current reference baseline. | EpsilonEnd 0.035. |
| epsend0035 | `refuted` | epsend004 retained. | Longer epsilon decay. |
| epsdecay300k | `refuted` | epsend004 retained. | Revisit penalty 0.12. |
| revisit012 | `refuted` | epsend004 retained. | Turn penalty 0.06. |
| turn006 | `refuted` | epsend004 retained. | Learning rate 0.000075. |
| lr75e5 | `refuted` | epsend004 retained. | Batch size 256. |
| bs256 | `partially_supported` | epsend004 retained. | Bounded learner/update comparison. |
| learner/update comparison | `partially_supported` | epsend004 retained. | RewardInfoScale 3.2 on updates1. |
| updates1_info32 | `partially_supported` | epsend004 retained; info32 strong candidate. | Terminal bonus 22. |
| terminal22 | `refuted` | epsend004 retained. | Timeout penalty 10. |
| timeout10 | `refuted` | epsend004 retained. | RewardInfoScale 3.1. |
| updates1_info31 | `partially_supported` | epsend004 retained; info31 strong candidate/base. | Step penalty 0.015. |
| step015 | `refuted` | epsend004 retained. | RewardInfoScale 3.05. |
| info305 | `refuted` | epsend004 retained. | Latest active: obstacle weight 0.20 on info31. |

## 5. Refuted Directions

| Direction group | Archived GPT conclusion |
| --- | --- |
| Budget extension and epsilon schedule search | 650k, epsilon_end 0.03, epsilon_end 0.035, and decay 300k were refuted or did not update baseline. |
| Revisit/turn shaping increases | RewardRevisitPenalty 0.12 and RewardTurnPenaltyScale 0.06 were refuted under epsend004 core. |
| Learner learning-rate reduction | LearningRate 0.000075 was refuted under epsend004 core. |
| Terminal/timeout pressure on info32 | RewardTerminalBonus 22 and RewardTimeoutPenalty 10 were refuted on updates1_info32. |
| Step-efficiency pressure on info31 | RewardStepPenalty 0.015 was refuted on info31. |
| Total info-scale reduction from info31 | RewardInfoScale 3.05 was refuted; epsend004 retained and info305 did not update baseline. |

## 6. Supported / Retained Directions

| Direction | Archived GPT conclusion |
| --- | --- |
| epsend004 retained as current train-side reference across refuted/partial branches | Retained through exploration, reward-shaping, learner/update, info32, info31, step015, and info305 branches. |
| EpsilonEnd 0.04 at 500k decay 240k min replay 8000 | Supported by epsend004 and remains the reference baseline. |
| LearnerUpdatesPerIter 1 branch | Bounded learner/update comparison made updates1 the strongest mechanism candidate but did not update baseline. |
| updates1_info32 | Strong candidate context; did not update reference baseline. |
| updates1_info31 | Strong candidate and current mechanism base; did not update reference baseline. |

## 7. Open Uncertainties

| Uncertainty | Navigation status |
| --- | --- |
| Information-reward composition on info31 | Active: test whether lower obstacle weight with RewardInfoScale 3.1 improves completion without losing info31 advantages. |
| Reward pressure trade-off | Terminal, timeout, step-penalty, and total info-scale moves were refuted; use only GPT-explicit bounded alternatives. |
| Learner/update dynamics | Candidate context remains useful if reward-composition tuning stalls; no active next surface unless GPT reopens it. |
| Posthoc/final_probe mismatch | Context only; not baseline decision authority. |
| Cross-seed robustness | Not evaluated by this single-seed map and not the default blocker. |

## 8. Next Candidate Surfaces

| Surface | Status from archived GPT review |
| --- | --- |
| `stable_updates1_info31_obst020_epsend004_budget500k_decay240k_minreplay8000_seed0` | Latest active candidate from info305 review. Parameter change: `RewardObstacleWeight: 0.25 -> 0.20`; restore `RewardInfoScale=3.1`; keep step penalty 0.02, timeout penalty 8, terminal bonus 20, updates per iter 1. Context: info31/info305 evidence chain with epsend004 retained as reference baseline. |

## 9. Maintenance Rule

- Update only from `history_index.json` entries and archived GPT tuning reviews.
- Keep current navigation, not repeated chronological prose.
- Aggregate retained-baseline outcomes once; do not append one row per archive.
- Remove or demote next surfaces after they are executed, refuted, or superseded unless GPT marks them pending/conditional.
- Do not duplicate full archived reviews, raw outputs, logs, CSVs, plots, trajectories, checkpoints, model weights, or commands.
