# Training System Manifest

## 1. Role

Engineering facts for GPT tuning review and Codex planning; not a tuning review and no parameter recommendations.

## 2. Repository Boundaries

| Repository label | Owns |
| --- | --- |
| `DRL-path-finding` source training repository | Training code, runtime behavior, generated outputs, checkpoints, full logs, full CSVs, plots, trajectories, and exact per-run artifact schemas. |
| `Automatic_2` | Compact implementation manifest and tracked evidence digests. |

Use repository-relative labels in reusable docs where possible. Keep private/local paths out of tracked history.

## 3. Current Mainline

| Surface | Current engineering fact |
| --- | --- |
| Main entry point | `train_q_agent.py` |
| Policy network | `agents/q_value_agent.py::ExplorationQNetwork` |
| State adapter | `agents/q_value_agent.py::StateTensorAdapter` |
| State representation | Shared semantic dual-state tensors: `advantage_canvas`, `value_block_features`, `value_entry_features`, `value_block_mask`, `value_entry_mask`. |
| Semantic layer | `env/shared_semantic_layer.py::SharedSemanticLayer` |
| Network shape | Dual-branch semantic dueling Q network: local decision canvas advantage branch plus accessible unknown block value branch. |
| Decision head | `heads/semantic_dueling_head.py::SemanticDuelingHead` combines `V(s)` and `A(s,a)` into 8-action Q values. |
| Learning algorithm | Double DQN with n-step transitions and uniform replay. |
| Formal protocol | `formal_posthoc_trainselect_v1`; current stable launcher uses train-side-only tuning, so posthoc/final_probe outputs are status-only in current factual reports. |

## 4. Training Execution Pipeline

1. `scripts/launch_formal_train_stable.ps1` starts `train_q_agent.py`.
2. `TransitionCollector` creates environment/map episodes and maintains cumulative exploration state.
3. `SharedSemanticLayer` plus `StateTensorAdapter` build semantic dual-state tensors.
4. `ExplorationQNetwork` plus `SemanticDuelingHead` produce masked 8-action Q values.
5. Epsilon-greedy rollout feeds n-step replay; `DDQNLearner` performs Double DQN updates.
6. Train-side monitoring records endpoint metrics, deltas, diagnostics, reward breakdown, learner/replay context, runtime, and artifacts under the source repository `outputs` tree.

## 5. State And Network Semantics

| Component | Engineering role | Diagnostic link |
| --- | --- | --- |
| `advantage_canvas` | Local occupancy/frontier/revisit/trajectory decision canvas. | Local action preference, turn burden, revisit behavior, stall, timeout. |
| `value_block_features` | Packed global accessible-unknown-block features. | Coverage and information-seeking context. |
| `value_entry_features` | Packed child frontier-entry features. | Unknown-area entry choices. |
| `value_block_mask`, `value_entry_mask` | Valid-block and valid-entry masks for packed tensors. | Variable block/entry packing semantics. |
| `SemanticDuelingHead` | Combines value-state `V(s)` and advantage-state `A(s,a)`. | 8-action Q values for greedy and epsilon-greedy policy paths. |

State/network structure affects coverage, information gain, revisit behavior, turn burden, stall, and timeout through learned action values.

## 6. Reward And Behavior Diagnostics Semantics

| Knob | Engineering role | Diagnostic links | Tuning risk |
| --- | --- | --- | --- |
| `RewardInfoScale` | Scales information-gain reward from empty and obstacle reveal components. | `info_reward_sum`, `weighted_info_gain_sum`, coverage, zero-info diagnostics. | Excessive information pressure can weaken terminal completion or timeout behavior. |
| `RewardStepPenalty` | Applies fixed per-step cost. | Episode length, reward, timeout, coverage gain per step. | High values can suppress useful exploration; low values can lengthen inefficient behavior. |
| `RewardTerminalBonus` | Adds success reward at coverage completion. | `success_rate`, `terminal_bonus_sum`, timeout, final coverage. | Excessive terminal pressure can dominate diagnostic reward components. |
| `RewardTimeoutPenalty` | Penalizes max-step timeout. | Timeout flag/rate, episode length, success_rate, coverage. | Excessive timeout pressure can distort long exploration episodes. |
| `RewardRevisitPenalty` | Penalizes recent-position revisits over the trajectory-history horizon. | `recent_revisit_trigger_count`, `recent_revisit_penalty_sum`, repeat visit ratio, stall. | Excessive revisit pressure can discourage necessary local maneuvering. |
| `RewardTurnPenaltyScale` | Scales angle-weighted turn penalty. | `turn_penalty_sum`, `turn_penalty_weight_sum`, turn burden, episode length. | Excessive turn pressure can reduce maneuverability around obstacles or frontiers. |
| `RewardObstacleWeight` | Weights obstacle reveal contribution inside information gain. | `obstacle_info_gain_sum`, `weighted_obstacle_info_gain_sum`, `obstacle_info_contribution_ratio`. | Excessive obstacle weighting can shift reward toward obstacle reveal over empty-space coverage. |

Diagnostic interpretation facts:
- `success_rate`, timeout, and `terminal_bonus_sum` indicate completion quality.
- Coverage and `info_reward_sum` indicate exploration/information capture.
- Episode length, repeat visit ratio, recent revisit, zero-info, stall, and turn burden indicate path efficiency and behavior quality.
- `td_abs_mean` and `grad_norm` are learner context, not standalone superiority evidence.

## 7. Stable Launcher And Launcher-Ready Surface

| Field | Fact |
| --- | --- |
| Launcher | `scripts/launch_formal_train_stable.ps1` |
| Command prefix | `.\scripts\launch_formal_train_stable.ps1` |
| Entry point | Runs `train_q_agent.py`. |
| Reproducibility/backend | Sets `PYTHONHASHSEED=0`, `CUBLAS_WORKSPACE_CONFIG=:4096:8`, strict reproducibility flags; disables TF32, cuDNN benchmark, AMP, inference AMP, `torch.compile`, and channels-last. |
| Seed policy | Fixed train episode and final-probe seed bases. |
| Train-side-only | Exposes `TrainSideOnlyTuning`; current formal train-side use keeps it enabled. |

Launcher-ready knobs:

| Group | Parameters |
| --- | --- |
| Run/execution | `RunName`, `Device`, `PythonExecutable`, `TrainSideOnlyTuning`, `DryRun` |
| Budget/seeding | `TotalEnvSteps`, `Seed`, `FixedTrainEpisodeSeedBase`, `FixedFinalProbeSeedBase`, `FinalGreedyEpisodes` |
| Exploration schedule | `EpsilonDecaySteps`, `EpsilonEnd` |
| Reward coefficients | `RewardRevisitPenalty`, `RewardTurnPenaltyScale`, `RewardTimeoutPenalty`, `RewardStepPenalty`, `RewardTerminalBonus`, `RewardInfoScale`, `RewardObstacleWeight` |
| Replay/learner | `BatchSize`, `ReplayCapacity`, `MinReplaySize`, `NStep`, `Gamma`, `LearningRate`, `TargetUpdateInterval`, `GradClipNorm` |
| Rollout/update cadence | `CollectStepsPerIter`, `LearnerUpdatesPerIter`, `TrainEveryEnvSteps` |

Use launcher-only `next_run_plan` only for stable-launcher or already supported CLI/config changes. If not exposed, request bounded config/launcher implementation; do not invent launcher flags.

## 8. Artifact And Monitoring Semantics

| Artifact | Engineering role |
| --- | --- |
| `logs/metric_snapshot.json` | Compact train-side metrics, deltas, diagnostics, learner/replay context, runtime, and evidence status. |
| `logs/config_snapshot.json` | Config, runtime, comparability, observed run contract, evaluation contract, reward semantics, and artifact references. |
| `logs/reproducibility_contract.json` | Reproducibility and launch contract: backend flags, seed policy, environment variables, sanitized argv, verdict. |
| `logs/artifact_index.json` | Artifact presence metadata. |
| `logs/train_steps.csv`, `logs/train_episodes.csv` | Source train-side monitoring CSVs summarized by Codex into current factual JSON. |
| `logs/posthoc_selection_summary.json`, `logs/final_probe_summary.json`, `logs/final_probe.csv` | Under current train-side-only factual reports, skipped/status-only and not performance evidence. |

Full logs, full CSVs, checkpoints, plots, trajectories, binary artifacts, and raw generated outputs remain in the source training repository.

## 9. Tuning Surface And Redesign Boundary

| Category | Examples | Boundary |
| --- | --- | --- |
| Launcher-ready knobs | Stable launcher groups in section 7. | Use only exposed launcher parameters in launcher-only plans. |
| CLI / `TrainConfig` knobs not exposed by stable launcher | `rows`, `cols`, `obstacle_ratio`, `scan_radius`, `max_accessible_blocks`, `max_entries_per_block`, posthoc interval/window fields. | Need supported CLI/config path or bounded launcher/config exposure. |
| Config-supported reward fields not exposed by stable launcher | `reward_turn_weight_45`, `reward_turn_weight_90`, `reward_turn_weight_135`, `reward_turn_weight_180`. | Do not invent stable launcher flags. |
| Config-only fields without direct CLI flag | `trajectory_history_steps`, `max_episode_steps`, `coverage_stop_threshold`, `weight_decay`. | Need bounded config/code exposure. |
| Runtime/profiling toggles | AMP, inference AMP, `torch.compile`, channels-last, TF32, cuDNN benchmark, timing/profiling, plot export, trajectory export. | Runtime speed alone is not system-performance superiority. |
| Method-level redesign boundary | Architecture, representation, semantic layer redesign, replay algorithm redesign beyond exposed config knobs, reward mechanism rewrite beyond coefficient tuning, action space changes, environment semantics changes. | Requires explicit discussion/approval; no unattended training command. |

Artifact comparability labels describe artifact comparison semantics. GPT must still respect launcher/config availability and method-level redesign boundaries.

## 10. Interpretation Rules

- Train-side monitoring is primary decision evidence; `posthoc` and `final_probe` are status-only under current train-side-only factual reports and not performance evidence.
- Reproducibility contract controls formal evidence admission.
- Runtime speed alone is not system-performance superiority.
- Full/raw artifacts stay in the source training repository, not `Automatic_2`.
- Manifest facts ground engineering feasibility; GPT decides from the current factual report, matched prior review, `tuning_map.md`, and this manifest.
