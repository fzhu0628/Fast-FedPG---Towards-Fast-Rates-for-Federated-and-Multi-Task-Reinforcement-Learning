# Fast-FedPG---Towards-Fast-Rates-for-Federated-and-Multi-Task-Reinforcement-Learning
## About this Paper
- This is the first published paper during my pursuit of the PhD degree.
- This paper was published at **IEEE Conference on Decision and Control** in 2024.
- This paper is a **pure theoretical** one.
- This work revolves around a _multi-agent reinforcement learning (MARL)_ setting, where each agent interacts with a **distinct** environment differing in the _regret function_ of its MDP.
- The collective **goal** of the agents is to _communicate intermittently_ via a central server to find a policy that **minimizes the average of long-term cumulative regret across environments**.
- A memory-based bias-correction policy gradient (PG) algorithm named "**_Fast-FedPG_**" is proposed, and we prove that under a _gradient-domination_ condition, Fast-FedPG guarantees
  - Fast **linear convergence** with exact gradients.
  - **Sub-linear rates** that enjoy a linear speedup w.r.t. the number of agents with noisy, truncated policy gradients.
  - **No heterogeneity bias** term is present in the result.
- Without gradient-domination, we establish convergence to a _first-order stationary point (FOSP)_ at a rate that continues to benefit from collaboration.
## Motivation
- Contemporary RL is challenging because of
  - High-dimensional data.
  - Massive state and action spaces.
  - Data hungry algorithms.
- **Proposal**: Reduce the **sample-complexity** for learning by using data collected from **diverse environments** in RL --- using federated RL (FRL).

![image](https://github.com/user-attachments/assets/dce81a33-a6e8-4157-b291-586bf7ae3f21)

- **Goals**:
  - To learn a policy that can perform well in all environments.
  - To demonstrate **collaborative speedup** in the **sample-complexity bounds**, i.e., multiple agents do help **expedite** the learning.
- Previous work in FRL fail to achieve **linear speedup** with **no additive heterogeneity bias**.
## Problem Setup
- Consider a setting of $N$ agents, each agent $i$ interacting with a **distinct** environment, characterized by MDP $M_i=(S,A,R_i,P,\gamma)$ with **finite** space and action spaces.
- Local loss function of agent $i$ playing policy $\pi$:

![image](https://github.com/user-attachments/assets/98f7fa66-77de-437a-9239-110545142ae0)

where $\rho$ is the distribution of the initial state.
- Policy gradient (PG): Parameterize the policy to optain $\pi_\theta$, and directly optimize $\theta$ to minimize the loss function $J_i(\theta)=J_i(\pi_\theta)$.
- The global loss function of interest:

![image](https://github.com/user-attachments/assets/2c98e698-04f4-4673-87c0-1a64bff5bbdd)

- **Constraints** in the problem:
  - Agent $i$ only has access to **noisy** and **truncated** gradient $\hat{\nabla}_K J_i(\cdot)$, where $K$ represents the length of each rollout.
  - Only **intermittent** communication once in every $H$ iterations is allowed.
## Presentation of the Fast-FedPG Algorithm
- **Communication constraints in FRL**: Server broadcasts $\bar{\theta}^{(t)}$ at round $t$. Agents initialize their local parameters to be $\bar{\theta}^{(t)}$ and perform $H$ local PG steps by interacting with their distinct environments.
- **Client-drift effect**: Since each agent updates towards its local optimum, a heterogeneity bias will occur that impedes convergence.

![image](https://github.com/user-attachments/assets/8cb66b3f-50d8-4d59-8a6a-d7680ed88a56)

- **Intuition of Fast-FedPG**: Ideally, the update equation would be

$\bar{\theta}^{(t+1)} = \bar{\theta}^{(t)} - \eta \hat{\nabla}_K J(\bar{\theta}^{(t)})$.

In this case, no heterogeneity bias is present since gradients are synchronized at every time-step.

- **Key idea of Fast-FedPG**: Use the **memory** of the global policy gradient $\hat{\nabla}_K J(\bar{\theta}^{(t)})$ to add the correction term $\hat{\nabla}_K J (\bar{\theta}^{(t)})-\hat{\nabla}_K J_i (\bar{\theta}^{(t)})$ to each local update:

![image](https://github.com/user-attachments/assets/051e46a7-684e-4f01-81e2-b0f8a2abb5a0)

  - _Observation_: Ideally (without noise and truncation), if we only have the blue term, then if $\theta_{i,\ell}^{(t)}$ is initialized at the optimum $\theta^{\*}$, then each agent still moves away from it since $\nabla J_i(\theta^*)\neq \theta^{\*}$. Thus, one can interpret the green term as the fixed-point correction term.
## Theoretical Results
### Assumptions
- The value function $J_i$ for each agent is $L$-smooth w.r.t. $\theta$.
- The variance of the noisy truncated gradient $\hat{\nabla}_K J_i(\cdot)$ is bounded by $\sigma^2$.
- The truncation error is at most $D\gamma^K$.
### Main challenges
- Effect of **regret-heterogeneity** & **intermittent communication**: Agents tend to drift towards their own locally optimal parameters.
- Effect of **non-convexity**: The value functions are non-convex.
- Effect of **noise and truncation**.
### Main results
- Under a suitable choice of step-size, Fast-FedPG guarantees under the gradient-domination condition:

![image](https://github.com/user-attachments/assets/22ad1df0-6ebe-4963-a1b5-45cfeb47874f)

where $G$ and $\mu$ are constants.

- Takeaways:
  * Up to round 𝑇, the total sample complexity is 𝑁𝐻𝑇, and our result features $\tilde{O}(1/(NHT))$, which is the best one could hope for.
  * It bridges the gap in the literature, where we have shown finite-time analysis with **linear speedup** and **no heterogeneity bias**.
  * Key helper result: **Average of PGs from different MDPs is the PG of a suitably defined “average MDP”** – allowing us to use the gradient-domination condition that ensures fast rates.

- Under a suitable choice of step-size, Fast-FedPG guarantees without the gradient-domination condition:

![image](https://github.com/user-attachments/assets/b7e7e60f-1503-46d0-9426-c939192c4bde)

- Takeaways:
  - Without the gradient-domination condition, our result still demonstrates a **$\sqrt{N}$-fold speedup** with **no heterogeneity bias**.

  - This result continues to hold even when the agents have different transition kernel $P_i$'s.


