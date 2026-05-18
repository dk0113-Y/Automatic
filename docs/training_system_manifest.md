# Training System Manifest

## 1. Role

This manifest is an engineering facts summary, not a tuning review. It records current implementation facts used by GPT tuning review and Codex execution planning for the DRL-path-finding training system.

It summarizes system structure, execution flow, state/network semantics, reward/diagnostic links, launcher/config availability, artifact roles, and redesign boundaries. It does not recommend parameter values.

## 2. Repository Boundaries

| Repository label | Owns |
| --- | --- |
| `DRL-path-finding` source training repository | Training code, runtime behavior, generated outputs, checkpoints, full logs, full CSVs, plots, trajectories, and exact per-run artifact schemas. |
| `Automatic_2` | Compact implementation manifest and tracked evidence digests. |

Use repository-relative labels in reusable docs where possible. Private/local paths should not be stored in tracked history.

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

Legacy near/mid/token and frontier-token paths are not the current method mainline.

## 4. Training Execution Pipeline

1. `scripts/launch_formal_train_stable.ps1` starts `train_q_agent.py`.
2. `TransitionCollector` creates environment/map episodes and maintains cumulative exploration state.
3. `SharedSemanticLayer` analyzes the cumulative map and agent state into accessible unknown block and frontier semantics.
4. `StateTensorAdapter` builds dual-state tensors for local advantage and global value branches.
5. `ExplorationQNetwork` and `SemanticDuelingHead` produce masked 8-action Q values.
6. Training rollout uses epsilon-greedy action selection under the legal-action mask.
7. Replay stores n-step transitions.
8. `DDQNLearner` performs Double DQN updates from replay.
9. Train-side monitoring records endpoint metrics, deltas, diagnostics, reward breakdown, learner/replay context, runtime, and artifact metadata.
10. Outputs are written under the source training repository `outputs` tree.

## 5. State And Network Semantics

| Component | Engineering role | Tuning interpretation link |
| --- | --- | --- |
| `advantage_canvas` | Local occupancy/frontier/revisit/trajectory decision canvas. | Affects learned local action preference, turn burden, revisit behavior, stall, and timeout through `A(s,a)`. |
| `value_block_features` | Packed global accessible-unknown-block features. | Affects learned global state value for coverage and information-seeking context. |
| `value_entry_features` | Packed child frontier-entry features for each block. | Affects learned access cues for unknown-area entry choices. |
| `value_block_mask`, `value_entry_mask` | Masks valid packed blocks and entries. | Preserve variable block/entry packing semantics during value encoding. |
| `SemanticDuelingHead` | Combines `V(s)` from value state with `A(s,a)` from advantage state. | Converts state structure into 8-action Q values used by greedy and epsilon-greedy policy paths. |

Diagnostic relevance: state/network structure affects coverage, information gain, revisit behavior, turn burden, stall, and timeout through learned action values.

## 6. Reward And Behavior Diagnostics Semantics

| Knob | Engineering role | Diagnostic links | Tuning risk |
| --- | --- | --- | --- |
| `RewardInfoScale` | Scales information-gain reward from empty and obstacle reveal components. | `info_reward_sum`, `weighted_info_gain_sum`, coverage, zero-info diagnostics. | Excessive information pressure can weaken terminal completion or timeout behavior. |
| `RewardStepPenalty` | Applies fixed per-step cost. | Episode length, reward, timeout, coverage gain per step. | Excessive step pressure can shorten behavior before useful exploration completes. |
| `RewardTerminalBonus` | Adds success reward at coverage completion. | `success_rate`, `terminal_bonus_sum`, timeout, final coverage. | Excessive terminal pressure can dominate diagnostic reward components. |
| `RewardTimeoutPenalty` | Penalizes max-step timeout. | Timeout flag/rate, episode length, success_rate, coverage. | Excessive timeout pressure can distort long exploration episodes. |
| `RewardRevisitPenalty` | Penalizes recent-position revisits over the trajectory-history horizon. | `recent_revisit_trigger_count`, `recent_revisit_penalty_sum`, repeat visit ratio, stall. | Excessive revisit pressure can discourage necessary local maneuvering. |
| `RewardTurnPenaltyScale` | Scales angle-weighted turn penalty. | `turn_penalty_sum`, `turn_penalty_weight_sum`, turn burden, episode length. | Excessive turn pressure can reduce maneuverability around obstacles or frontiers. |
| `RewardObstacleWeight` | Weights obstacle reveal contribution inside information gain. | `obstacle_info_gain_sum`, `weighted_obstacle_info_gain_sum`, `obstacle_info_contribution_ratio`. | Excessive obstacle weighting can shift reward toward obstacle reveal over empty-space coverage. |

Diagnostic interpretation facts:
- `success_rate`, timeout, and `terminal_bonus_sum` indicate completion quality.
- Coverage and `info_reward_sum` indicate exploration/information capture.
- Episode length, repeat visit ratio, recent revisit diagnostics, zero-info diagnostics, stall diagnostics, and turn burden indicate path efficiency and behavior quality.
- Learner diagnostics such as `td_abs_mean` and `grad_norm` are mechanism context, not standalone superiority evidence.

## 7. Stable Launcher And Launcher-Ready Surface

| Field | Fact |
| --- | --- |
| Launcher | `scripts/launch_formal_train_stable.ps1` |
| Command prefix | `.\scripts\launch_formal_train_stable.ps1` |
| Entry point | Runs `train_q_agent.py`. |
| Reproducibility | Sets `PYTHONHASHSEED=0` and `CUBLAS_WORKSPACE_CONFIG=:4096:8`; passes strict reproducibility flags. |
| Backend flags | Disables TF32, cuDNN benchmark, AMP, inference AMP, `torch.compile`, and channels-last. |
| Seed policy | Uses fixed train episode and fixed final-probe seed bases. |
| Train-side-only mode | Stable launcher exposes `TrainSideOnlyTuning` and current formal train-side use keeps it enabled. |

Launcher-ready knobs:

| Group | Parameters |
| --- | --- |
| Run/execution | `RunName`, `Device`, `PythonExecutable`, `TrainSideOnlyTuning`, `DryRun` |
| Budget/seeding | `TotalEnvSteps`, `Seed`, `FixedTrainEpisodeSeedBase`, `FixedFinalProbeSeedBase`, `FinalGreedyEpisodes` |
| Exploration schedule | `EpsilonDecaySteps`, `EpsilonEnd` |
| Reward coefficients | `RewardRevisitPenalty`, `RewardTurnPenaltyScale`, `RewardTimeoutPenalty`, `RewardStepPenalty`, `RewardTerminalBonus`, `RewardInfoScale`, `RewardObstacleWeight` |
| Replay/learner | `BatchSize`, `ReplayCapacity`, `MinReplaySize`, `NStep`, `Gamma`, `LearningRate`, `TargetUpdateInterval`, `GradClipNorm` |
| Rollout/update cadence | `CollectStepsPerIter`, `LearnerUpdatesPerIter`, `TrainEveryEnvSteps` |

GPT may generate a launcher-only `next_run_plan` only for changes supported by the stable launcher or an already supported CLI/config path. If a desired parameter is not exposed, GPT should request bounded config/launcher implementation work, not invent a launcher flag.

## 8. Artifact And Monitoring Semantics

| Artifact | Engineering role |
| --- | --- |
| `logs/metric_snapshot.json` | Compact train-side primary metrics, deltas, diagnostics, learner/replay context, runtime, and evidence status. |
| `logs/config_snapshot.json` | Config, runtime, comparability, observed run contract, evaluation contract, reward semantics, and artifact references. |
| `logs/reproducibility_contract.json` | Reproducibility and launch contract, including backend flags, seed policy, environment variables, sanitized argv, and verdict. |
| `logs/artifact_index.json` | Artifact presence metadata for required and optional outputs. |
| `logs/train_steps.csv` | Source run train-side step monitoring source; summarized by Codex into current factual JSON. |
| `logs/train_episodes.csv` | Source run train-side episode monitoring source; summarized by Codex into current factual JSON. |
| `logs/posthoc_selection_summary.json` | Under current train-side-only factual reports, skipped/status-only and not performance evidence. |
| `logs/final_probe_summary.json`, `logs/final_probe.csv` | Under current train-side-only factual reports, skipped/status-only and not performance evidence. |

Full logs, full CSVs, checkpoints, plots, trajectories, binary artifacts, and raw generated outputs remain in the source training repository.

## 9. Tuning Surface And Redesign Boundary

| Category | Examples | Boundary |
| --- | --- | --- |
| Launcher-ready knobs | Stable launcher groups in section 7. | GPT may use only exposed launcher parameters in launcher-only command plans. |
| CLI / `TrainConfig` knobs not exposed by stable launcher | `rows`, `cols`, `obstacle_ratio`, `scan_radius`, `max_accessible_blocks`, `max_entries_per_block`, posthoc interval/window fields. | Require supported CLI/config path or bounded launcher/config implementation before launch. |
| Config-supported reward fields not exposed by stable launcher | `reward_turn_weight_45`, `reward_turn_weight_90`, `reward_turn_weight_135`, `reward_turn_weight_180`. | Do not invent stable launcher flags. |
| Config-only fields without direct CLI flag | `trajectory_history_steps`, `max_episode_steps`, `coverage_stop_threshold`, `weight_decay`. | Require bounded config/code exposure before formal launcher use. |
| Runtime/profiling toggles | AMP, inference AMP, `torch.compile`, channels-last, TF32, cuDNN benchmark, timing/profiling, plot export, trajectory export. | Runtime speed alone is not system-performance superiority. |
| Method-level redesign boundary | Architecture, representation, semantic layer redesign, replay algorithm redesign beyond exposed config knobs, reward mechanism rewrite beyond coefficient tuning, action space changes, environment semantics changes. | Requires method-redesign discussion or bounded user-approved implementation; do not generate unattended training command. |

Artifact comparability labels such as `ALLOWED_TUNING_FIELDS`, `MANUAL_REVIEW_FIELDS`, and `FROZEN_COMPARABILITY_FIELDS` describe artifact comparison semantics. GPT must still respect launcher/config availability and method-level redesign boundaries.

## 10. Interpretation Rules

- Train-side monitoring is primary decision evidence.
- `posthoc` and `final_probe` are status-only under current train-side-only factual reports and are not performance evidence.
- The reproducibility contract controls formal evidence admission.
- Runtime speed alone is not system-performance superiority.
- Full/raw artifacts stay in the source training repository and are not copied to `Automatic_2`.
- Manifest facts ground engineering feasibility; GPT still makes tuning decisions from the current factual report, matched prior review, `tuning_map.md`, and this manifest.
