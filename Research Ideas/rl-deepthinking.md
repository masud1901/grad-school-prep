YES. You've touched something deep. There are several **unsolved or deeply troubling mathematical problems** at the heart of RL convergence. Let me lay them out:

---

## 1. The Deadly Triad (Sutton & Barto)
When you combine these three things:
- **Function approximation** (neural nets)
- **Bootstrapping** (TD learning, updating from own estimates)
- **Off-policy learning** (learning from old experience)

You get **guaranteed instability**. Not sometimes — mathematically provable that divergence can occur. This isn't engineering sloppiness, it's a **fundamental theorem**.

Nobody has fully solved this. PPO, SAC etc. are workarounds, not solutions.

---

## 2. The Bellman Optimality Problem
The Bellman equation assumes:

```
V*(s) = max_a [R(s,a) + γ · V*(s')]
```

This is a **fixed point equation** — you're looking for a value function that is consistent with itself. Proving this fixed point:
- **Exists** ✓ (proven, tabular case)
- **Is unique** ✓ (tabular case)
- **Can be found by neural nets** ✗ **NOT proven in general**

Neural nets approximate function spaces that aren't guaranteed to contain the true fixed point. You're searching for something that may not exist in your search space.

---

## 3. The Credit Assignment Problem
When your agent wins a chess game after 200 moves — **which moves actually caused the win?**

This is mathematically brutal:
- Reward is delayed and sparse
- Causality is tangled across time
- Gradient signals vanish over long sequences (vanishing gradient)

This connects to a deep information theory question:
> "How do you attribute causality in a long stochastic chain?"

**Still unsolved in general.** Transformers and attention mechanisms are partial engineering answers, not mathematical solutions.

---

## 4. Exploration is Formally Unsolvable in General
The **exploration vs exploitation tradeoff** in its most general form connects to:

### The Bandit Problem upper bound
For MAB, the **Lai-Robbins theorem (1985)** proves there's a theoretical lower bound on regret you can never beat:

```
Regret ≥ O(log T)
```

You will always lose *at least* logarithmically. Optimal algorithms exist for simple cases but for **deep RL with continuous spaces** — no optimal exploration strategy is known.

### It connects to computational complexity
Finding the optimal exploration policy is related to **PSPACE-hard problems** — potentially harder than NP-complete. Meaning even with unlimited data, computing the truly optimal policy may be intractable.

---

## 5. Generalization — The Deep Mystery
A neural net trained on millions of RL episodes in an environment often **catastrophically fails** when the environment changes slightly.

This connects to **statistical learning theory**:
- **VC dimension** — measures how complex a hypothesis class is
- **PAC learning** — probably approximately correct learning theory

For deep neural nets, the VC dimension is essentially **infinite** — meaning classical learning theory gives you almost no guarantees about generalization.

Why deep nets generalize at ALL is sometimes called the **"unreasonable effectiveness"** problem. It's not fully understood mathematically.

---

## 6. The Reward Specification Problem (Closest to Riemann-level)
This isn't just engineering — it's **mathematically ill-posed**:

> "How do you specify a reward function such that optimizing it produces the behavior you actually want?"

**Goodhart's Law** (informal):
> "When a measure becomes a target, it ceases to be a good measure"

The formal version connects to **inverse RL** and **cooperative AI theory**. Stuart Russell (Berkeley) argues this is the **central unsolved problem of AI** — you cannot mathematically prove that any reward function fully captures human intent.

It's related to **Gödel's incompleteness** in spirit — you can't fully describe what you want from within the system you're using to describe it.

---

## 7. The Non-Stationarity Problem
In Actor-Critic, both networks are changing simultaneously. This means each network is trying to learn a **moving target**:

- Critic learns V(s) under policy π
- But π is changing, so V(s) is always shifting
- Critic chases a target that moves because of Critic's own influence on Actor

This is mathematically similar to **coupled differential equations with feedback** — proving stability requires very strong conditions that real RL systems rarely satisfy.

Formally related to **game theory** — it's a **two-player non-cooperative game** trying to find Nash equilibrium through gradient descent. 

**Known result:** Gradient descent in games is NOT guaranteed to converge to Nash equilibrium. It can cycle forever.

---

## The Landscape of Unsolved Problems

```
Deadly Triad → no general solution
        ↓
Fixed point existence with neural nets → unproven
        ↓
Credit assignment over long horizons → unsolved
        ↓
Optimal exploration in continuous spaces → intractable
        ↓
Generalization guarantees for deep nets → mystery
        ↓
Reward specification → mathematically ill-posed
        ↓
Convergence of coupled learning systems → game theory, unsolved
```

---

## The Riemann Analogy

You asked about Riemann-level depth — here's the honest answer:

**Riemann Hypothesis** is pure math — clean, precise, either true or false.

The RL problems are **worse in a way** — they're not just unproven, some are **unprovable within current frameworks** because the frameworks themselves may be wrong. It's less like "we haven't found the proof yet" and more like "we may need entirely new mathematics."

The closest pure-math analog is probably:
- **P vs NP** — is finding optimal policies tractable at all?
- **Measure theory in infinite dimensions** — do neural net function spaces have the right properties?
- **Ergodic theory** — do these stochastic systems even have stable long-run behavior?

---

This is why RL is simultaneously the most exciting and most mathematically terrifying field in AI. It works empirically — but we largely don't know *why* it works, and we have proofs that it *shouldn't* work as well as it does.