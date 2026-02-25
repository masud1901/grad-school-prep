# Research Ideas: Adversarial Attacks on Federated Learning

**Author:** Md Akmol Masud  
**Supervisor:** Dr. Ning Lu, Queen's University  
**Date:** February 2026  
**Status:** Early brainstorming — to discuss with Prof. Lu

---

## Core Philosophy

> Instead of building better FL algorithms, exploit existing ones.  
> Break the system first, then propose the defense. That's two papers from one idea.

---

## Idea 1: Reconstructing Unseen Classes from Non-IID FL ⭐ (Most Novel)

### The Setup
- Non-IID (label-skewed) federated learning — the most common real-world scenario
- You are a participating client with only classes **A** and **B**
- Other clients have class **C** in their local data
- Through FL training, the global model learns about class C

### The Attack
**Reconstruct what class C data looks like — without ever seeing a single C sample.**

```
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│  Client YOU   │  │  Client 2    │  │  Client 3    │
│  Has: A, B    │  │  Has: C, D   │  │  Has: A, C   │
│  Never seen C │  │              │  │              │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘
       └────────┬────────┘─────────────────┘
                ▼
         ┌──────────────┐
         │ Global Model │  ← Knows A, B, C, D
         └──────────────┘

Attack: Extract class C representations from the global model
```

### Why It's Novel
- **Non-IID becomes the weapon, not the problem.** Label skew creates the information asymmetry that enables the attack.
- **The global model is the leak.** It carries learned representations of C embedded in weights — from other clients' private data.
- **Side information advantage.** Knowing A and B lets you isolate the "extra knowledge" in the global model that corresponds to C.
- **Nobody is doing exactly this.** Existing gradient inversion (Loki, DLG) reconstructs exact training images from gradients. This reconstructs an *entire unseen class*.

### Technical Approaches

**Method 1: Model Inversion**
- Optimize synthetic input `x*` such that global model classifies it as class C with maximum confidence
- `x* = argmax P(class=C | x, global_model)`

**Method 2: Delta Analysis (More Novel)**
- `Δ_weights = Global_Model - Local_Model`
- Local model knows A, B. Global model knows A, B, C, D.
- The delta encodes the "knowledge" of C and D from other clients
- Use this weight delta to guide generation of C samples

**Method 3: Round-by-Round Accumulation**
- Track how the model's "class C knowledge" grows across FL rounds
- Early rounds = weak signal, later rounds = full reconstruction
- Use the trajectory to progressively reconstruct C

### Paper Framing
**Title:** *"I've Never Seen Class C: Reconstructing Unseen Classes from Non-IID Federated Learning"*

**Punchline:** In non-IID FL, the very heterogeneity that makes training harder also makes privacy attacks worse. A client who only has classes A and B can reconstruct what class C looks like — data they were never supposed to see.

### Connection to Lab
- Mahdi's Paper #2 (Layer Normalization under Label Shift) — same non-IID setup, your attack exploits it
- Prof. Lu's FL over wireless work — attack works even with compressed updates
- Defense paper = natural follow-up publication

---

## Idea 2: Model Reconstruction from Compressed FL Updates

### The Setup
- Communication-efficient FL systems (DSFL, FLAC from Prof. Lu's lab)
- These compress model updates to save bandwidth
- People **assume** compression also provides privacy — but does it?

### The Attack
- As a participating client, accumulate partial/compressed updates across many rounds
- Use your local data as side information to "fill in the gaps"
- Over enough rounds, reconstruct the full global model despite compression

### Why It's Novel
- Targets Prof. Lu's own lab's systems (DSFL, FLAC) — immediately relevant
- Punchline: **"Communication efficiency ≠ privacy. Don't confuse compression with protection."**
- Client-side attack (most literature studies server-side attacks)

### Feasibility
- Low compute requirement — simulation only
- DSFL/FLAC code likely available
- Experiments on standard FL benchmarks (CIFAR-10, FEMNIST)

---

## Idea 3: Gaming the Client Selection Mechanism

### The Setup
- FL systems with intelligent client selection (like HeteRo-Select)
- Clients report metrics (data quality, compute capacity, network condition) to the scheduler

### The Attack (Two-Stage)
1. **Stage 1:** Report fake metrics to get selected disproportionately every round
2. **Stage 2:** Once selected, inject poisoned updates into the global model

### Why It's Novel
- Most attack papers assume random client selection
- Nobody asks "what if the attacker targets the scheduler itself?"
- Ties directly to your HeteRo-Select work — you built the scheduler, now you break it

### Defense → Second Paper
- After publishing the attack, propose a robust version of HeteRo-Select as the defense

---

## Comparison: Existing Work vs. Your Ideas

| Existing Work | Your Contribution | Gap |
|---------------|-------------------|-----|
| Gradient inversion (DLG, Loki — NeurIPS, IEEE S&P) | Client-side unseen class reconstruction | They reconstruct exact images. You reconstruct an entire unseen class |
| Model inversion on centralized models | Applied to non-IID FL | Nobody has exploited non-IID label skew as an attack vector |
| Membership inference | Class existence + reconstruction | They ask "was this sample in training?" You ask "what does this class look like?" |
| Secure aggregation defenses | Attacks despite compression | Compression for bandwidth ≠ privacy |

---

## Key Related Papers to Study

| Paper | Venue | Relevance |
|-------|-------|-----------|
| **Loki** (Large-scale Data Reconstruction) | IEEE S&P 2024 | State-of-the-art server-side attack — your benchmark |
| **DLG** (Deep Leakage from Gradients) | NeurIPS 2019 | Original gradient inversion — foundational |
| **InvertingGrad** | NeurIPS 2020 | Improved gradient inversion techniques |
| **Model Extraction on Split FL** | IJCAI 2024 | Closest to client-side extraction |
| **ACM Computing Surveys on FL Privacy** | ACM 2025 | Comprehensive survey — find your positioning |
| **FedREDefense** | ICML 2024 | Defense against model poisoning — shows what defenses exist |
| **Approx. Weighted Data Reconstruction** | 2025 | Latest attack on FedAvg — field is still active |

---

## Recommended Priority

1. **Idea 1** (Unseen class reconstruction) — Most novel, cleanest story, publishable at top venues
2. **Idea 2** (Compressed FL exploitation) — Directly ties to lab's work, strong practical impact
3. **Idea 3** (Client selection gaming) — Natural extension of HeteRo-Select, but more incremental

**Strategy:** Lead with Idea 1 as your thesis direction. Use Idea 2 as a supporting paper. Save Idea 3 for when you need a quick publication.

---

## Next Steps

- [ ] Discuss with Prof. Lu in first meeting (September 2026)
- [ ] Read Loki (IEEE S&P 2024) and DLG (NeurIPS 2019) for attack methodology
- [ ] Study Mahdi's label shift paper (#2) for the non-IID setup you'll exploit
- [ ] Build a simple FL simulation (FedAvg + non-IID split) as a testbed
- [ ] Attempt basic model inversion on the global model as proof-of-concept

---

*Created: February 26, 2026*  
*Purpose: Research brainstorming for MASc with Dr. Ning Lu @ Queen's University*
