# <a name="algorithms"></a>Algorithms (Theory)

The currently implemented algorithms combine deep neural networks with a number of classic reinforcement learning algorithms. These are just a starting point. Please invent your own! 

## What is reinforcement learning?

*Reinforcement learning (RL) is learning from interaction with an environment, from the consequences of action, rather than from explicit teaching. RL become popular in the 1990s within machine learning and artificial intelligence, but also within operations research and with offshoots in psychology and neuroscience.*

*Most RL research is conducted within the mathematical framework of Markov decision processes (MDPs). MDPs involve a decision-making agent interacting with its environment so as to maximize the cumulative reward it receives over time. The agent perceives aspects of the environment's state and selects actions. The agent may estimate a value function and use it to construct better and better decision-making policies over time.*

*RL algorithms are methods for solving this kind of problem, that is, problems involving sequences of decisions in which each decision affects what opportunities are available later, in which the effects need not be deterministic, and in which there are long-term goals. RL methods are intended to address the kind of learning and decision making problems that people and animals face in their normal, everyday lives.*

*- Rich Sutton*

For further reading on reinforcement learning see [David Silver's lectures](http://www0.cs.ucl.ac.uk/staff/d.silver/web/Teaching.html) and the book, [Reinforcement Learning: An Introduction, by Sutton and Barto](http://people.inf.elte.hu/lorincz/Files/RL_2006/SuttonBook.pdf).



## Why are deep neural networks useful for solving RL problems?

RL problems are characterized by incomplete information. The transition probabilities from one state to another given the action taken, for all states and actions are not known. Nor is the distribution of the rewards given a state and action. So in order to solve problems, RL algorithms involve approximating one or more unknown, typically complex, non linear functions. Deep neural networks make good candidates for these function approximators since they excel at approximating complex functions, particularly if the states are characterized by pixel level features.

For further reading on neural networks see [Neural Networks and Deep Learning](http://neuralnetworksanddeeplearning.com/)


## Terminology

- Agent: encapsulates a specific algorithm. Each agent has a policy, memory, optimizer, and preprocessor
- Online training: agents are trained periodically during episodes
- Episodic training: agents are only trained after an episode has completed and before the next episode begins
- Policy: rule which determines how to act in a given state, e.g. choose the action A which has the highest Q-value in state `S`. May be deterministic or stochatic. 
- Q function: `Q(S, A)`, estimates the value of taking action `A` in state `S` under a specific policy. 
- Q-vallue: Value of `Q` function for a particular `S` and `A`.
- On policy: the same policy is used to act and evaluate the quality of actions.
- Off policy: a different policy is used to act and evaluate the quality of actions. 


## <a name="families"></a>Families of RL Algorithms

To navigate the different RL algorithms:

- Model-free
    - Value-based
        - **DQN**
        - **DoubleDQN**
        - **Sarsa**
        - **OffpolSarsa**

    - Policy-based
        - **REINFORCE**

    - ActorCritic
        - **DPG** - typically implemented with ActorCritic, there's a different policy for exploration and target
        - **DDPG** - very similar to DPG except with target networks and batch normalization

- Model based
    - Value based
        - **Dyna**

    - Policy based
        - *pending*

    - ActorCritic
        - *pending*


## Implemented Algorithms

*See the overall list of agents and their implementation status under the [Agents Fitness Matrix](#agents-matrix). This section briefly explains the theories behind the implemented algorithms/agents.*


### <a name="q-learning"></a>Q-Learning

Q-learning algorithms attempt to estimate the optimal Q function, i.e the value of taking action `A` in state `S` under a specific policy. Q-learning algorithms have an implicit policy, typically `epsilon`-greedy in which the action with the maximum `Q` value is selected with probability `(1 - epsilon)` and a random action is taken with probability `epsilon`. The random actions encourage exploration of the state space and help prevent algorithms from getting stuck in local minima.

Q-learning algorithms are off-policy algorithms in that the policy used to evaluate the value of the action taken is different to the policy used to determine which state-action pairs are visited.

It is also a temporal difference algorithm. Updates to the `Q` function are based on existing estimates. The estimate in time t is updated using an estimate from time t+1. This allows Q-Learning algorithms to be online and incremental, so the agent can be trained during an episode. The update to `Q_t(S, A)` is as follows

![](./images/q_learning.png)

For more details, please see chapter 6 of [Reinforcement Learning: An Introduction, Sutton and Barto](http://people.inf.elte.hu/lorincz/Files/RL_2006/SuttonBook.pdf).

Since the policy that is used to evaluate the target is fixed (a greedy policy that selects the action that maximises the Q-value for a particular state) and is different to the policy used to determine which state-action pairs are being visited, it is possible to use experience replay to train an agent.

This is often needed for Agents to act in environments to get experiences. Experiences consist of the state, the action taken, the next state, and the reward, and are denoted as `<S_t, A_t, R_{t+1}, S_{t+1}>`. These experiences are stored in the agents memory. Periodically during an episode the agent is trained. During training n batches of size m are selected from memory and the `Q` update step is performed. This is different to Sarsa algorithms which are on-policy and agents are trained after each experience using only the most recent experience.

**Deep Q-Learning**

Standard Q-learning algorithm with experience replay. Online training every `n` experiences.

![](./images/deep_q_learning.png)

Agents: 

- `DQN`: function approximator - feedforward neural network
- `ConvDQN`: function approximator - convolutional network

**Double Q-Learning**

Q-learning algorithm with two `Q` function approximators to address the maximisation bias problem, `Q_1`, and `Q_2`. One `Q` function is used to select the action in the next state, `S'`, the other is used to evaluate the action in state `S'`. Periodically the roles of each `Q` function are switched. Online training every `n` experiences.

![](./images/double_q_learning.png)

Agents:

- `DoubleDQN`: function approximator - feedforward neural network
- `DoubleConvQN`: function approximator - convolutional network

**Deep Q-Learning with weight freezing**

Deep Q-Learning algorithms tends to be unstable. To address this issue, create two `Q` function approximators, one for exploration, `Q_e`, and one for evaluating the target, `Q_t`. The target is a copy of the exploration network with frozen weights which lag the exploration network.

These weights are updated periodically to match the exploration network. Freezing the target network weights help avoids oscillations in the policy, where slight changes to Q-values can lead to significant changes in the policy, and helps break correlations between the Q-network and the target. See [David Silver's](http://www0.cs.ucl.ac.uk/staff/d.silver/web/Resources_files/deep_rl.pdf) lecture slides for more details. Online training every n experiences.

![](./images/freeze_dqn.png)

 Agents:

- `FreezeDQN`: function approximator - feedforward neural network


### <a name="sarsa"></a>Sarsa

`Sarsa` algorithms also attempt to estimate the optimal `Q` function. They are on policy algorithms so the policy used to evaluate the target is the same as to the policy used to determine which state-action pairs are being visited.

Like Q-Learning, `Sarsa` is a temporal difference algorithm. However, since they are on policy, it is trickier to take advantage of experience replay, requiring storage of the action in state `t+1` and the Q-value for the state and action selected in `t+1` in an experience. In the following implementations, updates are made after each action with the exception of off policy expected Sarsa.

Sarsa update:

![](./images/sarsa.png)

This update is made each time the agent acts in an environment and gets an experience `<S_t, A_t, R_{t+1}, S_{t+1}>`

**Sarsa**

Standard Sarsa algorithm

![](./images/deep_sarsa.png)

Agents:

- `DeepSarsa`: function approximator - feedforward neural network

**Expected Sarsa**

Uses the expected value of the `Q` function under the current policy to construct the target instead of the Q-value for the action selected.

![](./images/expected_sarsa.png)

Agents

- `DeepExpectedSarsa`: function approximator - feedforward neural network


**Off Policy Expected Sarsa**

Sarsa is typically an on policy algorithm. However, if a different policy is used to evaluate the target than the one used to explore, it becomes and off-policy algorithm. With this set up, Q-Learning can be understood as a specific instance of Off Policy Expected Sarsa, when the policy used to evaluate the target is the greedy policy.

In off policy expected sarsa, actions are selected under the exploration policy, (annealling epsilon greedy for example).  Then the value of next state and action pair is calculated as the expected Q value under the target policy, for example epsilon greedy with some fixed value for epsilon.

`Q` update and translation to neural network update: Same as `ExpectedSarsa` with fixed epsilon.

Agents:

- `OffPolicySarsa`: function approximator - feedforward neural network


### <a name="pg"></a>Policy Gradient

*In progress*


### <a name="ddpg"></a>Deep Deterministic Policy Gradient

*In progress*


## RL Theory Resources

- [Demystifying Deep Reinforcement Learning](https://www.nervanasys.com/demystifying-deep-reinforcement-learning/): an introductory blog post about deep reinforcement learning; a good and short first intro for new comers.
- [Deep Reinforcement Learning](https://www.youtube.com/watch?v=PtAIh9KSnjo): 90 minute video overview by John Schulman. Assumes some knowledge of neural networks. If you are not familiar with neural networks, then start with Sutton and Barto’s book or David Silver's course
- [Reinforcement Learning: An Introduction](http://people.inf.elte.hu/lorincz/Files/RL_2006/SuttonBook.pdf): Classic textbook by Sutton and Barto. A good place to go next after watching John Schulman’s talk. Link is to the in progress 2nd edition. 
- [David Silver’s Reinforcement Learning Course](http://www0.cs.ucl.ac.uk/staff/d.silver/web/Teaching.html)
- [Playing Atari with Deep Reinforcement Learning, 2013](https://arxiv.org/abs/1312.5602): The famous paper from DeepMind describing a convolutional neural network Q-Learning architecture which learned to play seven different Atari games, achieving superhuman performance in three of them.
