# <a name="solutions"></a>Solutions

Agents and best solutions by OpenAI Lab users. We want people to start from working solutions instead of stumbling their ways there.

## Submission instructions

If you invent a new algorithm/combination that beats the best solutions, please submit a [Pull Request](https://github.com/kengz/openai_lab/pulls) to OpenAI Lab. Refer to the [PR template](https://github.com/kengz/openai_lab/blob/master/.github/PULL_REQUEST_TEMPLATE.md) for the submission guideline. See examples from the accepted [solution PRs](https://github.com/kengz/openai_lab/pulls?q=is%3Apr+label%3Asolution+is%3Aclosed).

To learn how to analyze experiment data, refer to [Analysis](#analysis).


## <a name="fitness-matrix"></a>Fitness Matrix

A matrix of the best `fitness_score` of **Agents** v.s. **Environments**, sourced from the accepted [solution PRs](https://github.com/kengz/openai_lab/pulls?q=is%3Apr+label%3Asolution+is%3Aclosed). See [Metric](#metrics) for the design of fitness score and generalized metrics.

||DQN|DoubleDQN|Sarsa|OffPolicySarsa|ActorCritic|DDPG|
|:---|:---|:---|:---|:---|:---|:---|
|**CartPole-v0**|[9.635032](https://github.com/kengz/openai_lab/pull/73)|[10.34826](https://github.com/kengz/openai_lab/pull/78)|-|[12.98525](https://github.com/kengz/openai_lab/pull/91)|[13.90989](https://github.com/kengz/openai_lab/pull/123)|-|
|**CartPole-v1**|[13.22935](https://github.com/kengz/openai_lab/pull/80)|[16.06697](https://github.com/kengz/openai_lab/pull/82)|-|[18.91624](https://github.com/kengz/openai_lab/pull/107)|[30.57067](https://github.com/kengz/openai_lab/pull/124)|-|
|**Acrobot-v1**|[-0.1051617](https://github.com/kengz/openai_lab/pull/94)|[-0.1045992](https://github.com/kengz/openai_lab/pull/98)|-|[-0.1127294](https://github.com/kengz/openai_lab/pull/97)|[-0.1175654](https://github.com/kengz/openai_lab/pull/128)|-|
|**MountainCar-v0**|-|[-0.03744196](https://github.com/kengz/openai_lab/pull/117)|-|-|-|-|
|**MountainCarContinuous-v0**|*n/a*|*n/a*|*n/a*|*n/a*|-|-|
|**Pendulum-v0**|*n/a*|*n/a*|*n/a*|*n/a*|-|[-0.2696825](https://github.com/kengz/openai_lab/pull/130)|
|**LunarLander-v2**|[2.786624](https://github.com/kengz/openai_lab/pull/84)|[2.992104](https://github.com/kengz/openai_lab/pull/87)|-|[3.313421](https://github.com/kengz/openai_lab/pull/96)|-|-|
|**LunarLanderContinuous-v2**|*n/a*|*n/a*|*n/a*|*n/a*|-|[-0.03972519](https://github.com/kengz/openai_lab/pull/133)|
|**BipedalWalker-v2**|-|-|-|-|-|-|
|**BipedalWalkerHardcore-v2**|-|-|-|-|-|-|
|**CarRacing-v0**|-|-|-|-|-|-|
|**AirRaid-v0**|-|-|-|-|-|-|
|**Alien-v0**|-|-|-|-|-|-|
|**Assault-v0**|-|-|-|-|-|-|
|**Breakout-v0**|-|-|-|-|-|-|
|**MsPacman-v0**|-|-|-|-|-|-|
|**Pong-v0**|-|-|-|-|-|-|
|**Qbert-v0**|-|-|-|-|-|-|
|**SpaceInvader-v0**|-|-|-|-|-|-|
|**FlappyBird-v0**|-|-|-|-|-|-|
|**Snake-v0**|-|-|-|-|-|-|


## <a name="agents-matrix"></a>Agents Fitness Matrix

A projection of the Fitness Matrix along the Agents axis. This shows overall status of the Agents in OpenAI Lab. Feel free to invent new ones! For more details, see [Algorithms](#algorithms) and [Families of RL Algorithms](#families).

_Pending: we have a generic formalization to cross-evaluate Agents using heatmap statistics; see [Metrics](#metrics). This is on the roadmap._

|algorithm|implementation|eval score (pending)|
|:---|:---|:---|
|[DQN](https://arxiv.org/abs/1312.5602)|[DQN](https://github.com/kengz/openai_lab/blob/master/rl/agent/dqn.py)|-|
|[Double DQN](https://arxiv.org/abs/1509.06461)|[DoubleDQN](https://github.com/kengz/openai_lab/blob/master/rl/agent/double_dqn.py)|-|
|[Dueling DQN](https://arxiv.org/abs/1511.06581)|-|-|
|Sarsa|[DeepSarsa](https://github.com/kengz/openai_lab/blob/master/rl/agent/deep_sarsa.py)|-|
|Off-Policy Sarsa|[OffPolicySarsa](https://github.com/kengz/openai_lab/blob/master/rl/agent/offpol_sarsa.py)|-|
|[PER (Prioritized Experience Replay)](https://arxiv.org/abs/1511.05952)|[PrioritizedExperienceReplay](https://github.com/kengz/openai_lab/blob/master/rl/memory/prioritized_exp_replay.py)|-|
|[CEM (Cross Entropy Method)](https://en.wikipedia.org/wiki/Cross-entropy_method)|next|-|
|[REINFORCE](http://incompleteideas.net/sutton/williams-92.pdf)|-|-|
|[DPG (Deterministic Policy Gradient) off-policy actor-critic](http://jmlr.org/proceedings/papers/v32/silver14.pdf)|[ActorCritic](https://github.com/kengz/openai_lab/blob/master/rl/agent/actor_critic.py)|-|
|[DDPG (Deep-DPG) actor-critic with target networks](https://arxiv.org/abs/1509.02971)|[DDPG](https://github.com/kengz/openai_lab/blob/master/rl/agent/ddpg.py)|-|
|[A3C (asynchronous advantage actor-critic)](https://arxiv.org/pdf/1602.01783.pdf)|-|-|
|Dyna|next|-|
|[TRPO](https://arxiv.org/abs/1502.05477)|-|-|
|Q*(lambda)|-|-|
|Retrace(lambda)|-|-|
|[Neural Episodic Control (NEC)](https://arxiv.org/abs/1703.01988)|-|-|
|[EWC (Elastic Weight Consolidation)](https://arxiv.org/abs/1612.00796)|-|-|


## <a name="environments-matrix"></a>Environments Fitness Matrix

A projection of the Fitness Matrix along the Environments axis. This shows the best solutions for the environments. The list of accepted solutions can be seen in the [solution PRs](https://github.com/kengz/openai_lab/pulls?q=is%3Apr+label%3Asolution+is%3Aclosed).


### Classic Environments

|problem|fitness score|epis before solve / best 100-epi mean|author|experiment_spec|
|:---|:---|:---|:---|:---|:---|
|CartPole-v0|[13.90989](https://github.com/kengz/openai_lab/pull/123)|3|kengz/lgraesser|cartpole_ac_softmax|
|CartPole-v1|[30.57067](https://github.com/kengz/openai_lab/pull/124)|3|kengz/lgraesser|cartpole_v1_ac_softmax|
|Acrobot-v1|[-0.1045992](https://github.com/kengz/openai_lab/pull/98)|-104.34|kengz/lgraesser|acrobot_offpol_sarsa|
|MountainCar-v0|[-0.03744196](https://github.com/kengz/openai_lab/pull/117)|970|kengz/lgraesser|mountain_double_dqn|
|MountainCarContinuous-v0|-|-|-|-|
|Pendulum-v0|[-0.2696825](https://github.com/kengz/openai_lab/pull/130)|-137.3549|kengz/lgraesser|pendulum_ddpg_per_linearnoise|


### Box2D Environments

|problem|fitness score|epis before solve / best 100-epi mean|author|experiment_spec|
|:---|:---|:---|:---|:---|:---|
|LunarLander-v2|[3.313421](https://github.com/kengz/openai_lab/pull/96)|200|kengz/lgraesser|lunar_offpol_sarsa|
|LunarLanderContinuous-v2|[-0.03972519](https://github.com/kengz/openai_lab/pull/133)|503|kengz/lgraesser|lunar_cont_ddpg_linearnoise|
|BipedalWalker-v2|-|-|-|-|
|BipedalWalkerHardcore-v2|-|-|-|-|
|CarRacing-v0|-|-|-|-|


### Atari Environments

|problem|fitness score|epis before solve / best 100-epi mean|author|experiment_spec|
|:---|:---|:---|:---|:---|:---|
|AirRaid-v0|-|-|-|-|
|Alien-v0|-|-|-|-|
|Assault-v0|-|-|-|-|
|Breakout-v0|-|-|-|-|
|MsPacman-v0|-|-|-|-|
|Pong-v0|-|-|-|-|
|Qbert-v0|-|-|-|-|
|SpaceInvader-v0|-|-|-|-|


### PyGame Environments

|problem|fitness score|epis before solve / best 100-epi mean|author|experiment_spec|
|:---|:---|:---|:---|:---|:---|
|FlappyBird-v0|-|-|-|-|
|Snake-v0|-|-|-|-|


### Universe Environments

|problem|fitness score|epis before solve / best 100-epi mean|author|experiment_spec|
|:---|:---|:---|:---|:---|:---|
|-|-|-|-|-|

