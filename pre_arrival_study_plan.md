# Pre-Arrival Study Plan: March → August 2026
**Md Akmol Masud** | Incoming MASc, Queen's ECE (Supervisor: Dr. Ning Lu)  
**Goal:** Arrive in September ready to contribute to research from day one.  
**Pace:** ~12 hrs/week average | ~227 hrs total work | ~85 hrs buffer for busy weeks

---

## The Core Principle

> Certs are a byproduct, not the goal. Study to become dangerous as a researcher
> in Prof. Lu's lab — not to collect credentials. Every hour should make you
> more capable of engaging with Dr. Lu's bandit papers, Mahdi's FL work,
> and your own thesis direction from day one.

---

## Overview: What & Why

| Track | Course | Why It Matters |
|---|---|---|
| **Foundation** | Math for ML & DS — Calculus + Probability | Notation fluency for everything else |
| **Core Theory** | UAlberta RL Specialization (4 courses) | Formal language of your thesis direction |
| **Core Theory** | Boyd's Convex Optimization (Stanford) | Convergence proofs, theoretical FL work |
| **Lab Context** | Computer Communications (U. Colorado) | Wireless network layer Prof. Lu assumes you know |
| **Lab Context** | Key Papers (Mahdi + Prof. Lu's INFOCOM) | Direct preparation for lab discussions |

---

## Phase 1 — Clear the Foundation
### March 1 → March 31 (4 weeks | ~48 hrs available)

**Objective:** Finish Math for ML & DS so notation never slows you down again.
Complete RL Course 1 so you enter Phase 2 with MDP fundamentals solid.

| Week | Focus | Hours | Milestone |
|---|---|---|---|
| Week 1 (Mar 1–7) | Math: Calculus — finish remaining ~50% | 12 hrs | Gradients, chain rule, Hessians feel geometric |
| Week 2 (Mar 8–14) | Math: Probability & Statistics — begin | 12 hrs | Random variables, expectations, conditional probability |
| Week 3 (Mar 15–21) | Math: Probability & Statistics — finish | 11 hrs | ✅ Math for ML & DS **COMPLETE** |
| Week 4 (Mar 22–31) | RL Course 1 — finish remaining ~50% | 8 hrs | ✅ RL Course 1 **COMPLETE** |

**Key mindset for Calculus:** You're not reinventing the wheel. Extract geometric
intuition — gradient as steepest ascent direction, Hessian as curvature measure,
chain rule as why backprop works. Don't go deeper than that.

**Key mindset for Probability:** This is the decoder ring for MDP notation.
Every $\mathbb{E}[\cdot]$, $P(s'|s,a)$, and $\sum$ you'll see in the RL course
lives in what this section teaches. Connect them actively as you go.

---

## Phase 2 — Build the RL Core
### April 1 → May 15 (6 weeks | ~72 hrs available)

**Objective:** Complete RL Courses 2 and 3 — the most technically powerful material.
This is where your client scheduling thesis direction becomes implementable,
not just conceptual. Run Boyd in parallel at a lighter pace.

| Week | Focus | Hours | Milestone |
|---|---|---|---|
| Week 5 (Apr 1–7) | RL Course 2 (Sample-based Methods) — begin | 12 hrs | TD learning, Monte Carlo, Q-learning |
| Week 6 (Apr 8–14) | RL Course 2 — finish | 12 hrs | ✅ RL Course 2 **COMPLETE** |
| Week 7 (Apr 15–21) | RL Course 3 (Function Approximation) — begin | 12 hrs | Value function approx with neural nets |
| Week 8 (Apr 22–28) | RL Course 3 — continue | 12 hrs | Policy gradient methods, Actor-Critic |
| Week 9 (Apr 29–May 5) | RL Course 3 — finish | 10 hrs | ✅ RL Course 3 **COMPLETE** |
| Week 10 (May 6–15) | Buffer / review week + Boyd chapters 1-2 | 10 hrs | Convex sets, convex functions — foundations |

**Why Course 3 is critical for you specifically:**  
Function approximation is where the bandit-FL connection becomes a real system.
Your client scheduler can't enumerate all possible client states — their data
distribution, battery, network condition form a continuous space. Course 3 teaches
you to approximate value functions over exactly this kind of space using neural
networks. This is your MASc thesis in embryonic form.

**Boyd parallel track (Weeks 5–10):**  
Don't stop Boyd. Aim for ~2 hrs/week during this phase — just enough to keep
momentum. Focus on Chapters 1–3: convex sets, convex functions, and convex
optimization problems. These chapters are where you build the "eye for convexity"
that makes you dangerous as an applied scientist.

---

## Phase 3 — Systems Layer + RL Capstone
### May 16 → June 30 (7 weeks | ~84 hrs available)

**Objective:** Complete Computer Communications so the wireless network layer
Prof. Lu assumes clicks into place. Complete RL Course 4 capstone —
treat it as a prototype of your thesis work. Accelerate Boyd.

| Week | Focus | Hours | Milestone |
|---|---|---|---|
| Week 11 (May 16–22) | Comms Course 1 (Network Fundamentals) | 12 hrs | OSI layers, error control, protocol design |
| Week 12 (May 23–29) | Comms Course 2 (Peer-to-Peer & LANs) | 12 hrs | MAC protocols, wireless LANs |
| Week 13 (May 30–Jun 5) | Comms Course 3 (Packet Switching) | 12 hrs | Routing, congestion control, graph theory |
| Week 14 (Jun 6–12) | Comms Course 4 (TCP/IP & Advanced) | 12 hrs | ✅ Computer Communications **COMPLETE** |
| Week 15 (Jun 13–19) | RL Course 4 Capstone — begin | 12 hrs | Formalize FL scheduling as your RL problem |
| Week 16 (Jun 20–26) | RL Course 4 Capstone — finish | 12 hrs | ✅ RL Specialization **COMPLETE** |
| Week 17 (Jun 27–30) | Buffer week + Boyd Chapters 4–5 | 8 hrs | Duality, KKT conditions — begin |

**Capstone strategy:** Don't use the default capstone problem.
Frame your own: federated client scheduling as an MDP.
- **State:** Global model quality + client availability profile
- **Action:** Which $k$ clients to select this round
- **Reward:** Reduction in global loss after aggregation
- **Policy:** What HeteRo-Select currently does heuristically

Implementing this, even roughly, means you arrive at Queen's with a
working prototype of your thesis direction. That's not a student exercise —
that's preliminary research.

---

## Phase 4 — Deepen Boyd + Read the Papers
### July 1 → August 31 (9 weeks | ~108 hrs available)

**Objective:** Push Boyd through duality and first-order methods — the chapters
most directly relevant to FL convergence proofs. Do the deep paper reading
that no course can substitute. Read Slivkins as the bridge between course-level
RL and publication-level bandit theory.

| Week | Focus | Hours | Milestone |
|---|---|---|---|
| Week 18 (Jul 1–7) | Boyd Chapters 5–6: Duality + KKT conditions | 12 hrs | Lagrangian, dual problem, KKT — core of FL proofs |
| Week 19 (Jul 8–14) | Boyd Chapter 9: Unconstrained minimization | 12 hrs | Gradient descent, Newton's method |
| Week 20 (Jul 15–21) | Boyd Chapter 10: Equality constrained problems | 10 hrs | The theory behind FedProx and constrained FL |
| Week 21 (Jul 22–28) | Paper: Mahdi's IoTJ Survey (deep read) | 10 hrs | ✅ Lab worldview mapped |
| Week 22 (Jul 29–Aug 4) | Paper: FLAC — read convergence proof carefully | 10 hrs | ✅ Can follow Prof. Lu's proof style |
| Week 23 (Aug 5–11) | Paper: Constrained Bandit w/ Switching Costs | 10 hrs | ✅ Bandit framework for client scheduling clear |
| Week 24 (Aug 12–18) | Paper: Backlogged Bandits (INFOCOM) | 10 hrs | ✅ Queueing + bandit fusion understood |
| Week 25 (Aug 19–25) | Slivkins: "Introduction to Multi-Armed Bandits" (arXiv) | 10 hrs | ✅ Regret bounds, UCB theory — pub-level bandit theory |
| Week 26 (Aug 26–31) | Review + synthesis week | 8 hrs | Write 1-page note connecting all 4 papers to your thesis |

**The synthesis note (Week 26):** Write one page — for yourself, not for anyone
else — answering: "How do Prof. Lu's bandit papers, Mahdi's FL work, and my
HeteRo-Select paper connect into a single research direction?" This exercise
forces integration of everything you've learned and gives you a draft of what
to say in your first meeting with Prof. Lu.

---

## Completion Summary

| Course / Material | Target Completion | Status |
|---|---|---|
| Math for ML & DS — Calculus | Week 1 (Mar 7) | 🔄 In progress |
| Math for ML & DS — Probability | Week 3 (Mar 21) | ⬜ Not started |
| UAlberta RL — Course 1 | Week 4 (Mar 31) | 🔄 In progress |
| UAlberta RL — Course 2 | Week 6 (Apr 14) | ⬜ Not started |
| UAlberta RL — Course 3 | Week 9 (May 5) | ⬜ Not started |
| Boyd's Convex Optimization | Week 20 (Jul 21) | 🔄 In progress |
| Computer Communications | Week 14 (Jun 12) | ⬜ Not started |
| UAlberta RL — Course 4 Capstone | Week 16 (Jun 26) | ⬜ Not started |
| Mahdi IoTJ Survey | Week 21 (Jul 28) | ⬜ Not started |
| FLAC Paper | Week 22 (Aug 4) | ⬜ Not started |
| Constrained Bandit Paper | Week 23 (Aug 11) | ⬜ Not started |
| Backlogged Bandits Paper | Week 24 (Aug 18) | ⬜ Not started |
| Slivkins MAB Introduction | Week 25 (Aug 25) | ⬜ Not started |
| Synthesis Note (1 page) | Week 26 (Aug 31) | ⬜ Not started |

---

## Key Papers & Links

| Resource | Link | Priority |
|---|---|---|
| Mahdi's IoTJ FL Survey | https://ieeexplore.ieee.org/abstract/document/10153432 | 🔴 Critical |
| FLAC Paper | https://ieeexplore.ieee.org/abstract/document/10000743 | 🔴 Critical |
| Slivkins MAB Introduction | https://arxiv.org/abs/1904.07272 | 🔴 Critical |
| Mahdi's Lab Page | https://mahdibeit.github.io/ | 🟡 Reference |
| Boyd Textbook (free PDF) | https://web.stanford.edu/~boyd/cvxbook/ | 🔴 Critical |
| Boyd Lecture Videos | https://www.youtube.com/playlist?list=PL3940DD956CDF0622 | 🟡 Supplement |
| Sutton & Barto RL Textbook (free) | http://incompleteideas.net/book/the-book.html | 🔴 Critical |

---

## Weekly Rhythm (Suggested)

To make 12 hrs/week sustainable across 26 weeks, a consistent daily rhythm
works better than marathon weekend sessions:

- **Weekdays (Mon–Fri):** 1.5 hrs/day = 7.5 hrs
- **Weekend (Sat or Sun):** 1 session of ~4.5 hrs = 4.5 hrs
- **Total:** ~12 hrs/week

On moderate-commitment weeks, drop the weekend session and protect
the weekday rhythm. Never miss two weekdays in a row.

---

## What You're Building

Each phase builds a layer:

```
August 2026:
┌─────────────────────────────────────────────┐
│  VISION: See FL scheduling as a bandit MDP  │  ← Papers + Synthesis
├─────────────────────────────────────────────┤
│  FRAMEWORKS: RL theory + Convex optim.      │  ← UAlberta + Boyd
├─────────────────────────────────────────────┤
│  SYSTEMS: Wireless network layer            │  ← Comms specialization
├─────────────────────────────────────────────┤
│  LANGUAGE: Math notation fluency            │  ← Math for ML & DS
└─────────────────────────────────────────────┘
```

By September 2026, you walk into Prof. Lu's lab able to:
- Read his INFOCOM bandit papers without skipping the math
- Follow convergence proofs in FL papers at NeurIPS/ICML level
- Speak the wireless networking language his lab uses daily
- Sketch the MDP formulation of your thesis direction at a whiteboard
- Have a working prototype RL scheduler from the capstone

That is what "useful from day one" looks like.

---

*Last updated: February 2026*  
*Total estimated hours: ~227 hrs | Available hours: ~312 hrs | Buffer: ~85 hrs*
