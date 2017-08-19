---
title: OpenAI Lab 手册

language_tabs:

toc_footers:
  - <a href='https://github.com/kengz/openai_lab'>OpenAI Lab Github</a>
  - <a href='https://github.com/openai/gym'>OpenAI Gym Github</a>
  - <a href='https://github.com/fchollet/keras'>Keras Github</a>
  - <a href='https://youtu.be/qBhLoeijgtA'>RL Tutorial video part 1/2</a>
  - <a href='https://youtu.be/wNSlZJGdodE'>RL Tutorial video part 2/2</a>

includes:
  - INSTALLATION
  - USAGE
  - EXPERIMENTS
  - ANALYSIS
  - SOLUTIONS
  - METRICS
  - ALGORITHMS
  - AGENTS
  - DEVELOPMENT
  - CONTRIBUTING
  - MOTIVATIONS

search: true
---

# OpenAI Lab </br> [![GitHub release](https://img.shields.io/github/release/kengz/openai_lab.svg)](https://github.com/kengz/openai_lab) [![CircleCI](https://circleci.com/gh/kengz/openai_lab.svg?style=shield)](https://circleci.com/gh/kengz/openai_lab) [![Codacy Badge](https://api.codacy.com/project/badge/Grade/9e55f845b10b4b51b213620bfb98e4b3)](https://www.codacy.com/app/kengzwl/openai_lab?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=kengz/openai_lab&amp;utm_campaign=Badge_Grade) [![Codacy Badge](https://api.codacy.com/project/badge/Coverage/9e55f845b10b4b51b213620bfb98e4b3)](https://www.codacy.com/app/kengzwl/openai_lab?utm_source=github.com&utm_medium=referral&utm_content=kengz/openai_lab&utm_campaign=Badge_Coverage) [![GitHub stars](https://img.shields.io/github/stars/kengz/openai_lab.svg?style=social&label=Star)](https://github.com/kengz/openai_lab) [![GitHub forks](https://img.shields.io/github/forks/kengz/openai_lab.svg?style=social&label=Fork)](https://github.com/kengz/openai_lab)

_For English doc, refer to the original [OpenAI Lab doc.](http://kengz.me/openai_lab/)_

_使用 OpenAI Gym，Tensorflow 和 Keras 的加强学习 (Reinforcement Learning) 实验框架。_

_OpenAI Lab__让人们把加强学习 (Reinforcement Learning / RL) 像科学一样来 _理论，实验_。 它为 [OpenAI Gym](https://gym.openai.com/) 和 [Keras](https://keras.io/) 提供了一个简单的界面， 并具有自动化的实验和评估框架。

### 特征

1. 使用 OpenAI Gym，Tensorflow，Keras 的**统一RL环境和界面**，因此您可以专注于研究算法。
2. **[核心RL算法](#agents-matrix), 和可重复使用的模块化组件** 来研究深度RL算法.
3. **[实验框架](#experiments)** 能够运行数百次超参数优化试验，具有用于测试新的RL算法的日志，绘图和分析。 实验设置存储在标准化的JSON中，可用于重现性和比较。
4. **[实验的自动分析](#analysis)** 用于评估RL代理和环境，并帮助选择最佳解决方案。
5. **[Fitness Matrix](#fitness-matrix)**, 一个RL算法最佳分数表 v.s. 环境; 有助于研究。


使用OpenAI Lab，我们可以专注于研究强化学习的基本要素，如算法，策略，内存和参数调优。 它允许我们使用现有组件与研究思想的实现有效地构建代理。 然后，我们可以通过运行实验系统地测试研究假设。

*详细了解 Lab 在动机 [Motivations](#motivations) 中的研究问题。 最终，Lab 是进行强化学习的广义框架，与 OpenAI Gym 和 Keras 无关系。 例如。 基于Pytorch的实现在路线图上。*


### 实现算法

实现/计划的核心RL算法的列表。

要看看他们对 OpenAI Gym 环境的分数，请去 **[Fitness Matrix](#fitness-matrix)**.


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


### 运行 Lab

接下来，看安装 [Installation](#installation) 并跳转到快速入门 [Quickstart](#quickstart).


<div style="max-width: 100%"><img alt="Timelapse of OpenAI Lab" src="./images/lab_demo_dqn.gif" /></div>

*Timelapse of OpenAI Lab, solving CartPole-v0.*

