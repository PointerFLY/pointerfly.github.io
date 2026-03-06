---
layout: post
title: A Theory of Vibe Coding
mathjax: true
date: 2026-03-01 21:25 +0800
---

# Overview

This article is trying to propose a theoretic framework to think about Large Language Model (LLM) and the concept Vibe Coding concept through the lens of probability and stochastic process.

It is important to emphasize that this is merely one perspective. The true nature of LLMs is complex, multi-disciplinary and still underdevelopment, but mapping their behavior to stochastic processes helps software engineers demystify the mechanics of modern "vibe coding."

# LLM: Probabilistic Models

To establish this framework, we must first accept a core premise: we are treating the Large Language Model strictly as a probabilistic model. Just as early theoretical work on Convolutional Neural Networks (CNNs) sought to understand deep learning through Empirical Risk Minimization (ERM) rather than biological mimicry, we must strip away anthropomorphic analogies. When an agent writes code, it is fundamentally minimizing a loss function over a distribution of tokens, not "thinking" in the human sense.

Formally, an LLM learns a parameterized probability distribution $P_\theta(X)$ to approximate the true data distribution $P_{data}(X)$ over sequences of tokens $X$. During training, this is typically achieved via Maximum Likelihood Estimation (MLE), minimizing the cross-entropy loss:

$$ \mathcal{L}(\theta) = -\mathbb{E}_{X \sim P_{data}}[\log P_\theta(X)] $$

This view is supported by recent research, such as studies highlighting the *[Illusion of Thinking](https://machinelearning.apple.com/research/illusion-of-thinking)* in LLMs. Models often fail catastrophically when superficial details of a logic puzzle are altered, suggesting that what appears to be formal reasoning is actually sophisticated, probabilistic pattern matching.  
By acknowledging this illusion, we can stop asking "why didn't the AI understand my prompt?", start asking "how do I shift the probability distribution $P_\theta(Y|X)$ to favor the correct output $Y$?"

# The Basic: Autoregressive Token Generation

At the foundational level, an LLM defines a conditional probability distribution over a vocabulary $V$. The generation of the output text is an autoregressive stochastic process, sampling from the following distribution iteratively until an End-of-Sequence (EOS) token is reached:

$$ P(w_{n+1} \mid w_1, \dots, w_n), \; w_{n+1} \in V $$

Each generated token $w_{n+1}$ depends on the entire preceding sequence, making it a highly complex, non-Markovian process at the token level (though bounded by the context window).

# Conversation: Autoregressive Text Generation

In the early ChatGPT era, interactions were purely conversational without tool use. The user provides an input sequence (prompt) $X = (x_1, \dots, x_m)$, and the model generates a response sequence $Y = (y_1, \dots, y_k)$.

Given the finite length of the input token stream due to the context window limit, we can simplify the generation into a conditional probability distribution over sequences. Let $S_t$ represent the entire context state at turn $t$ (including all previous conversation history). The transition to the next state $S_{t+1}$ (which includes the user's new prompt and the model's response) is given by:

$$ P(S_{t+1} \mid S_t), \; S_{t+1} \in \mathcal{L} $$

where $\mathcal{L}$ is the space of all valid token sequences within the context window. The model generates the response $Y_t$ autoregressively:

$$ P(Y_t \mid S_t) = \prod_{i=1}^{|Y_t|} P_\theta(y_{t,i} \mid S_t, y_{t,<i}) $$

This process is a straightforward sequence-to-sequence mapping, heavily reliant on the internal weights $\theta$ to hallucinate or recall correct information.

# Agent: Interaction Beyond the LLM

Agents complicate the base process by extending the capabilities of the LLM through the introduction of external tools. A tool can be modeled as a deterministic or stochastic function $T: \mathcal{X} \to \mathcal{Y}$ that accepts a string $x$ and generates a new string $y$:

$$ y = T(x) $$

When an LLM decides to emit a special token sequence denoting a tool call action $A_t$, the autoregressive generation pauses. The environment executes $T(A_t)$ and returns an observation $O_t$. This observation is appended back to the context window:

$$ S_{t+1} = S_t \oplus A_t \oplus O_t $$

This fundamental shift alters the loop of the LLM. It is no longer a closed-system generative model relying solely on $P_\theta$; it is now an open system where external computational entropy and deterministic facts are injected into the context window, dynamically shifting the conditional probabilities for subsequent generation.

# Agent Loop: Markov Decision Process

When we introduce "iterations" (e.g., compiling, running tests, reading error logs, and refining code), the system evolves from a simple random walk into a Markov Decision Process (MDP), or more accurately, a Partially Observable Markov Decision Process (POMDP):

```python
state = build_initial_context(prompt, codebase)

while not has_reached_absorbing_state(state):
    # LLM acts based on current context: A_t ~ \pi_\theta(A_t | S_t)
    action = llm.sample_action(state) 

    # Environment evaluates the action deterministically: O_t = T(A_t)
    observation = environment.execute(action) 
    
    # Context update: S_{t+1} = M(S_t, A_t, O_t)
    state = context_manager.update(state, action, observation) 
```

- **State ($S_t$)**: The current context window. This includes the initial natural language prompt, the current draft of the codebase, and the latest environmental feedback (error logs, test results, or linter output).
- **Action ($A_t$)**: The LLM sampling a new sequence from its policy $\pi_\theta(A_t \mid S_t)$. This could be writing a patch, replacing a file, or executing a tool call.
- **Transition Dynamics ($P(S_{t+1} \mid S_t, A_t)$)**: The feedback from the environment. The environment evaluates the action and transitions the system to a new state. Since the environment (e.g., a test suite) is deterministic, the transition is largely defined by the tool's output.
- **Reward ($R_t$)**: A sparse, environmental signal, such as +1 for passing all tests and 0 otherwise.

**Crucial Distinction: In-Context Traversal vs. RL Training**
It is vital to clarify that in standard Vibe Coding, the LLM's weights $\theta$ are **frozen**. The agent is *traversing* this POMDP using its pre-trained, fixed policy $\pi_\theta$. There is no reinforcement learning, gradient descent, or backpropagation happening during the loop. The "learning" is entirely **in-context**. The agent reaches the goal not by updating its internal parameters to maximize a reward function, but by exploring the state space via context accumulation until it discovers an action trajectory that triggers the termination signal (success).

# Iterative Refinement as Bayesian Inference

Every iteration where the agent writes code, executes it, and receives an error message can be viewed as a Bayesian update.

Let $C$ be the space of all possible code implementations. Initially, the LLM has a prior distribution $P(C \mid S_0)$ based solely on the prompt and its pre-training.

When the agent executes the code action $A_t$, it receives an observation $O_t$ (e.g., a stack trace or a failed unit test). The likelihood of observing this specific output given a code implementation is $P(O_t \mid C)$.

The agent updates its belief over the correct code using Bayes' Theorem:

$$ P(C \mid S_t, O_t) \propto P(O_t \mid C) P(C \mid S_t) $$

- **Prior**: The agent's current distribution over the correct implementation, $P(C \mid S_t)$.
- **Observation**: The execution output $O_t$.
- **Posterior**: The updated distribution over possible correct codes, $P(C \mid S_{t+1})$. By feeding the observation back into the context window, the agent conditions its next sample on the fact that its previous hypothesis was incorrect. The observation $O_t$ acts as evidence, collapsing the probability mass around hypotheses that are consistent with resolving the error. This iteratively reduces the entropy $H(C)$ of the solution space.

# Convergence

In the context of Vibe Coding, convergence is defined as the agent reaching an **absorbing state** $S^\ast$ where the task is complete. Mathematically, an absorbing state is one where the transition probability to any other state is zero: $P(S^\ast \mid S^\ast, A) = 1$, and the agent's policy outputs a "terminate" action with probability 1.

Practically, convergence means the code meets a predefined termination criteria:

1. **Functional Completeness**: All unit, integration, and e2e tests pass (the environment returns a success signal).
2. **Human Approval**: The code passes visual and architectural inspection by a human developer.

It is crucial to note that for any set of functional requirements, there are theoretically infinitely many valid code implementations $C_{valid} \subset C$ that satisfy the automated test suite. A naive MDP process might converge to any random absorbing state within $C_{valid}$. However, a well-steered MDP process aims to converge the end state strictly into a much smaller, optimal subset $C_{optimal} \subset C_{valid}$. This subset represents implementations that yield a high Value function from a software engineering perspective—characterized by low structural entropy, high maintainability, and scalability.

The efficiency of vibe coding is measured by the **expected hitting time** $\mathbb{E}[T_{S^\ast}]$, which is the expected number of iterations (tool calls and generations) required to reach the absorbing state $S^\ast$. A lower hitting time implies a faster and more efficient agent loop.

# HITL (Human-in-the-Loop)

Without human steering, an autonomous coding agent is highly susceptible to converging on a "local minimum"—an absorbing state where the unit tests pass and the code compiles, but the architecture is an unmaintainable disaster (e.g., spaghetti code, hardcoded values). In an MDP landscape, the agent has reached a state that satisfies the automated environment's sparse reward signal (passing tests) but fails on unstated, long-term objectives (maintainability).

The human developer acts as an external **Oracle** and a **Control Mechanism**, forcefully altering the transition probabilities of the MDP. By reviewing the code and injecting a mid-prompt (e.g., "Refactor this to use the Strategy pattern"), the human:

1. **Shifts the Prior**: Injects strong structural priors into the context window, radically altering $P(C \mid S_t)$.
2. **Escapes Local Minima**: Forces a state transition away from the suboptimal absorbing state, $P(S_{new} \mid S_{local}, A_{human}) = 1$, pushing the agent back into exploration.
3. **Acts as a Surrogate Reward**: Since the agent cannot be trained via RL on a complex reward function on the fly, the human provides heuristic "pseudo-rewards" directly in natural language. These constraints for Code Quality, Security, and Scalability act as new deterministic rules in the context window.

Through human steering, the search space is pruned, drastically reducing the expected hitting time $\mathbb{E}[T_{S^\ast}]$ and ensuring the final state is a global optimum rather than a local one.

# Senior Software Engineer

A highly experienced senior software engineer is critical to successful vibe coding because they possess a highly refined, internal prior distribution $P_{senior}(C)$ over optimal software architectures.

In reinforcement learning terms, a senior engineer possesses an accurate **Value Function** $V^\ast(S_t)$. They can look at an intermediate state (a drafted pull request) and accurately estimate the long-term cost of that code. Because the agent itself cannot learn a value function for the specific codebase on the fly (its weights are frozen), the senior engineer lends their internal value function to the agent. When they steer the agent, their injected prompts act as heuristic guidance, substituting for the dense reward signals that a formal RL agent would otherwise require during training.

Because they can accurately foresee architectural dead-ends, they can prune massive branches of the agent's search tree early. This accelerates the convergence (minimizing $\mathbb{E}[T_{S^\ast}]$) and guarantees that the absorbing state $S^\ast$ represents high code quality, adhering to strict non-functional requirements.

# Junior Software Engineer

Conversely, new software engineers are in a disadvantaged position for vibe coding. The knowledge required to effectively steer a Coding Agent takes years of empirical training to acquire.

A rookie developer possesses an uncalibrated prior $P_{rookie}(C)$ and a value function $V_{rookie}(S_t)$ with high variance. They may not recognize when the agent is heading towards an architectural local minimum. Consequently, they might accept a suboptimal absorbing state $S_{local}$ simply because the tests pass.

Furthermore, when a rookie attempts to steer the agent, their prompts ($A_{human}$) may introduce noise rather than reducing entropy. Unlike machines, human intuition cannot be fine-tuned via backpropagation overnight. Without the seasoned intuition to provide precise Bayesian updates to the agent's context, the rookie-agent loop may diverge, resulting in endless iterations of broken code.
