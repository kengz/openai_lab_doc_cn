# <a name="metrics"></a>Metrics

The Lab setup allows us to run experiments at scale; the standardized framework also allows us to reliably compare multiple agents (algorithms) and environments (problems). These are shown with the [Fitness Matrix](#fitness-matrix), which also necessitates a higher level evaluation metric.

With the Lab, we are breeding multiple agents across many environments and selecting the best ones. Naturally, this selection metric is called the `fitness_score`. Some evolutionary search algorithm for the `HyperOptimizer` is on our [roadmap](#roadmap).

The Fitness Matrix is a projection from the parameter space of each agent-environment pair, where each matrix cell is the highest fitness score the agent could achieve in the environment.

To understand the bigger picture, the domain for the fitness function for each matrix cell is the parameter space of the agent conditioned on the environment. Inside the parameter space, each point gets mapped to a fitness score.

To analogize, see fitness score as temperature, then we have a heatmap inside the parameter space, and we are searching for the hottest point and recording that in a cell of the Fitness Matrix.

In this section, we will formalize these ideas.


## <a name="fitness"></a>Fitness Score

The fitness function `f` is the base function behind the Fitness Matrix and the fitness heatmap. It computes the fitness score for each point (trial) in a parameter space.

The fitness function's design is motivated by the need to have a richer evaluation of an agent, and the ability of the Lab to provide such data. We felt that the `mean rewards` metric used in the OpenAI gym evaluation is insufficient, but it is included in our design under *strength*.

The fitness score of a trial is as follows:

`fitness_score = mean_rewards_per_epi_mean * [(1+stability_mean) * ((1+consistency)**2)] ** sign(mean_rewards_per_epi_mean)`

or renaming variables by what the terms represent:

`fitness_score = power * distinguisher`

where

- `power = mean_rewards_per_epi_mean`
- `distinguisher = amplifier ** sign(power)`
- `amplifier = (1+stability_mean) * ((1+consistency)**2)`

The fitness score is designed to capture the following:

1. **strength**: `mean_rewards` (over the past 100 episodes)
2. **speed**: `1/epi`
3. **stability**: session-level stability, `stable_epi_count / mastery_gap`
4. **consistency**: AKA trial-level stability, `solved_ratio_of_session`
5. **granularity**: in `1+stability` and `1+consistency` to ensure partial solution doesn't get lost when stability and consistency = 0
6. **amplification**: amplify by session-level stability linearly with `*(1+stability)`, and by trial-level stability quadratically with `*(1+consistency)**2`
7. **distinguishability**: multiply amplifier if `power` is positive, else divide; essentially `*(amplifier**sign(power))`


### Strength

The higher (more positive) the `mean_rewards` an agent gets, the stronger it is. The mean is taken over the past 100 episodes, as common to OpenAI gym.

### Speed

Given two same `mean_rewards`, the agent that achieves it in less episodes is faster, with `speed = 1/epi`. This yields the notion of `power = strength * speed = mean_rewards_per_epi`. Use the sessions-mean of a trial, i.e. `mean_rewards_per_epi_mean` = mean of multiple `mean_rewards_per_epi` values.

### Stability

`stability = stable_epi_count / mastery_gap` for a session, with range `0.0 - 1.0` from unstable to stable. Measures session-level stability. Use the sessions-mean of a trial, i.e. mean of multiple `stability` values.

- `stable_epi_count` = the number of stable episodes, where stable means an episode passes the `r_threshold`.
- `r_threshold` = `sys_vars['SOLVED_MEAN_REWARD']` if the problem is solvable; `max_rewards - (0.10 * (max_rewards-min_rewards))` otherwise.
- `first_solved_epi` = first episode that passes the `r_threshold`.
- `mastery_gap` = `last_epi - first_solved_epi` normally, or `INF` to yield stability 0.0 if `first_solved_epi` is undefined because no episode passes the threshold.

The reasoning for the definition is simple: regardless if the problem is solved or not, we want to see if the agent could stability its performance. Once it solves (for solvable problems) or achieves a sufficiently high score (unsolved problems), its `mean_rewards` every episode should not drop too much, even when accounting for random fluctuations. We can define a minimal threshold (10% less than the ideal) that the subsequent episodes should surpass. Then, the stability is simple the ratio of the number of episodes that pass the threshold after the first solution.


### Consistency

`consistency = solved_ratio_of_session` for a trial, with range `0.0 - 1.0` from inconsistent to consistent. This is the trial-level measurement of stability, as it measures how consistently the agent can solve an environment given multiple repeated sessions.

`consistency = 0` always for unsolved problems (unbounded rewards) since solution is undefined.

### Granularity

When `stability=0` or `consistency=0` the multiplier will drop to zero, regardless if there is any partial solutions. This will make training harder as the agent will not learn from partial solutions if these are treated as non-solutions. So, restore the granularity that preserves partial solution simply by adding 1, i.e. `1+stability` and `1+consistency`.

### Amplification

To separate solutions from noise, amplify the good ones and separate them out, while diminish and cluster the worse ones together. Amplify by session-level stability linearly with `*(1+stability)` since it's of the same order as `power`. Amplify by trial-level stability quadratically with `*(1+consistency)**2` since trial stability is of the next order. Amplifier is always positive.

### Distinguishability

Always amplify to make better solutions have more positive fitness score. If `power` is negative, amplify toward the 0 axis, i.e. divide by amplifier. If `power` is positive, amplify away from the 0 axis, i.e. multiply the amplifier. Essentially, `distinguisher = amplifier**sign(power)`.


## Generalized Metrics

With the fitness function defined above, we can evaluate a single agent in a single environment. In fact, this is a single experiment with multiple trials, and we compute a fitness score per trial, using the trial's parameter values. We then pick the max fitness score for the Fitness Matrix.

Given that the Lab can run multiple agents across environments in its standardized framework, naturally we ask:

- we can evaluate **one agent in one environment**, (experiment metrics)
- how do we evaluate and compare **multiple agents in one environment**? (environment metrics)
- how about **one agent in multiple environments**? (agent metrics)
- how about the universal-view of **multiple agents in multiple environments**? (universal metrics)

This section formalizes the generalized metrics for these, and shows that the Fitness Matrix is just a max-function projection of some higher dimensional space.


### <a name="generalization"></a>Generalization

We include the formalization of evaluation metrics across the Agent space and Environment space, and present a generalization, which produces the [Fitness Matrix](#fitness-matrix).

*We're using LaTeX for better symbolic rendering.*

![](./images/metric_1.png)
![](./images/metric_2.png)
![](./images/metric_3.png)
![](./images/metric_4.png)
