# Reinforcement learning overview

Reinforcement learning (RL) is learning by **trial and error against an environment**: an agent takes actions, gets rewards, and improves. Unlike supervised learning there are no labels — the feedback is a scalar reward that's often delayed and sparse.

## The MDP formalism

An RL problem is a **Markov decision process** — a tuple `(S, A, P, R, γ)`:

- **S** — states: everything the agent can observe (`s_t`).
- **A** — actions: what the agent can do (`a_t`).
- **P** — transition function: `P(s' | s, a)`, how the environment responds.
- **R** — reward function: `r_t` the agent receives.
- **γ** — discount factor: how much future rewards are worth vs now (0 = greedy, 1 = count everything).

The agent's goal: maximize **expected discounted return** `Σ_t γ^t r_t`, not the immediate reward.

## Value functions, policies, and the exploration/exploitation tradeoff

- **Policy** `π(a|s)` — what the agent does in a state. Deterministic `a = π(s)` or stochastic.
- **Value function** `V(s)` — expected return starting from state `s`. **Q-function** `Q(s, a)` — expected return from state `s` *taking action `a` first*. The agent's decision is: pick `a` that maximizes `Q(s, a)`.
- **Exploration vs exploitation**: should the agent take the action it thinks is best (exploit) or try something new that might be better (explore)? The whole field is about balancing this — ε-greedy (random with probability ε), optimistic initialization, UCB.

## Q-learning: from table to network

**Q-learning** learns `Q` by **temporal-difference (TD) updates**:

```
Q(s,a) ← Q(s,a) + α ( r + γ · max_a' Q(s', a') − Q(s,a) )
```

- It's **off-policy**: the update uses the *best* next action `max_a' Q`, even when the agent acts randomly (ε-greedy). It learns from experience produced by any behavior.
- Classic version keeps a **Q-table** (`num_states × num_actions`) — fine for tiny toy grids, impossible for real problems.
- **DQN** (Deep Q-Network) replaces the table with a neural net `Q_θ(s, a)` and adds two stabilizers: a **target network** (frozen copy for the bootstrap term) and a **replay buffer** (break correlation between consecutive samples). That's the jump that made RL work on Atari.

## Policy gradients

Instead of learning values and acting greedily on them, learn the **policy directly**: REINFORCE updates parameters in the direction that increases the probability of actions that led to high return:

```
θ ← θ + α · G_t · ∇_θ log π_θ(a_t | s_t)
```

The variance problem — different random rollouts give different `G_t` — is fixed by **baselines** (subtract a value estimate), and modern methods (PPO, SAC) combine policy + value learning.

## Why it's a big separate field

- **No labels, only rewards** — the training signal is sparse, delayed, and non-stationary (the data distribution changes as the agent improves, unlike supervised i.i.d. data).
- Needs environment interaction, which is slow and often risky/expensive in the real world (simulation is the usual answer).
- Different evaluation culture: no fixed test set — you report learning curves over environment seeds.
- It shows up in ML careers mostly as **specialized**: robotics, games/Atari, recommender/systems optimization (contextual bandits), LLM alignment via RLHF ([instruction-tuning-rlhf](../nlp/instruction-tuning-rlhf.md)). The bandit/contextual version is the common "RL-adjacent" job skill.

## Related

- [reptile-meta-rl](../automl/reptile-meta-rl.md) — meta-learning for RL (adapt a policy to new tasks fast).
- [PLAN](../) — where this fits in the ML breadth plan.
