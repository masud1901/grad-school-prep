Let me re-read the survey carefully and systematically map the future directions.Good — I already have the full survey content from the context window. Let me now work directly from the paper's content to identify research directions, and cross-reference with current literature.Good. Now I have everything I need. Let me map the survey's gaps directly against what's still open in 2025.

---

## Research Directions from the Beitollahi-Lu Survey — Mapped to Open Problems

I'll go section by section through the survey's flagged gaps, then tell you which ones are **still genuinely open** in 2025 and how they connect to your background.

---

### Direction 1: Tolerating Inaccurate vs. Unsuccessful Transmissions — *The "How Much Noise Can FL Handle?" Problem*

**What the survey says:** Section IV raises a specific unanswered question — *"Is it better for FL to receive an inaccurate model or no model at all? How many dropouts can the network tolerate before convergence breaks?"* Wei and Shen showed that SNR needs to scale as O(t²) to maintain convergence, but the dropout side remains unresolved.

**What's still open in 2025:** This question remains largely unanswered in the non-IID heterogeneous setting. Most work assumes either perfect transmission or a simple dropout model — nobody has jointly characterized the *combined* effect of noise + dropout + non-IID data on convergence bounds.

**Why it fits you:** Your HeteRo-Select work already deals with heterogeneous clients. Extending that to model the transmission reliability of each client — and designing a scheduler that accounts for both *data quality* and *channel reliability* — is a natural next paper. The bandit framing works perfectly here: reward = gradient quality × transmission success probability.

**Possible title direction:** *"When to Wait and When to Skip: Scheduling Policies for FL under Joint Transmission Noise and Client Dropout"*

---

### Direction 2: Analog vs. Digital Co-Design for Non-IID Data — *The Practical Gap Nobody Has Closed*

**What the survey says:** Section V concludes that analog over-the-air schemes are superior in bandwidth efficiency but require tight synchronization and aren't backward-compatible with existing digital infrastructure. The survey explicitly says the analog scheme "is more prone to slow users" and that the non-IID + analog combination needs more work.

**What's still open in 2025:** Over-the-air FL leverages the superposition feature of multi-access channels to achieve efficient low-latency global model aggregation, but combining this with heterogeneous non-IID data distributions and dynamic client participation remains an active open problem. Almost all OTA-FL papers assume IID data or homogeneous devices.

**Why it fits you:** This is more of a wireless systems problem than a pure ML problem — it sits at exactly the intersection Prof. Lu specializes in. If you want a paper that *directly* exploits your lab's expertise, this is it. The contribution would be designing a scheduling/power-control policy for OTA-FL that handles non-IID data distributions without degrading the superposition property.

**Risk level:** High technical difficulty — requires PHY layer knowledge. But high reward if done well (IEEE TWC, JSAC territory).

---

### Direction 3: Asynchronous FL with Fast-User Utilization — *The "Don't Waste Fast Clients" Problem*

**What the survey says:** Section VII, point 3 explicitly asks: *"How can we utilize the resources of fast users without biasing the final model toward the faster user's data?"* This is the fundamental tension in asynchronous FL — if you let fast clients update more often, the global model drifts toward their data distribution.

**What's still open in 2025:** Asynchronous federated policy gradient methods achieve linear speedup in sample and time complexity compared to synchronous methods, especially in scenarios with high computing power heterogeneity — but this is in the RL setting. In standard FL with non-IID data, the bias-from-fast-clients problem is still unsolved in the wireless network context specifically.

**Why it fits you:** This connects beautifully to your multi-armed bandit interest. You can frame it as: *"fast clients are high-frequency arms — how do you exploit them without letting them dominate the reward signal?"* A staleness-weighted bandit scheduler that dynamically adjusts how much influence fast clients have based on their data distribution similarity to the global distribution is a clean, novel idea.

**Possible title direction:** *"Bias-Aware Asynchronous Client Scheduling for FL over Wireless Networks via Bandit Optimization"*

---

### Direction 4: Heterogeneous Model FL with Knowledge Distillation over Wireless — *The "Not Everyone Has the Same Model" Problem*

**What the survey says:** Section VII, point 4 flags that allowing different model sizes/architectures across clients is unsolved, and that knowledge distillation is the only real tool available but "the best strategy for the network to enable and accommodate knowledge transfer" remains unclear.

**What's still open in 2025:** Semi-federated learning frameworks allow computing-scarce users to participate by sending raw data to the base station for remote computing, integrating centralized and federated learning into a unified framework — but this still assumes homogeneous model architectures. True heterogeneous model FL (different architectures per client) over wireless networks with communication constraints is wide open.

**Why it fits you:** Your healthcare AI background gave you experience with model compression and efficiency (MosQNet-SA used depthwise separable convolutions for a 10× parameter reduction). You understand the tradeoff between model complexity and accuracy. A knowledge distillation framework that adapts distillation intensity based on wireless channel conditions is a concrete, feasible contribution.

---

### Direction 5: Scalability to Millions of Devices — *The "FedAvg Doesn't Scale" Problem*

**What the survey says:** Section VII, point 5 is blunt — FL experiments max out at a few hundred devices. Scaling FedAvg to millions of devices with all the wireless challenges simultaneously is "an unresolved question."

**What's still open in 2025:** Achieving scalable, reliable, and communication-efficient FL remains an open research problem, especially in cross-device scenarios where devices frequently join and leave the network. In large-scale cross-device settings, K can be in the millions, but only a small fraction of clients are active and eligible at any given time.

**Why it fits you:** This is actually where your hierarchical proxy idea (Direction from our earlier conversation) fits most naturally. Your proposed system *is* a scalability solution — by collapsing K clients into M proxies, you make the coordinator's problem tractable. The scalability angle is your strongest pitch to Prof. Lu: *"Here's how we get from hundreds of devices to millions."*

---

## Summary Table for You

| Direction | Survey Section | Open in 2025? | Fits Your Background? | Difficulty | Venue Potential |
|---|---|---|---|---|---|
| Noise vs. dropout tolerance | §IV | ✅ Yes | ✅ Strong (HeteRo-Select) | Medium | IEEE TWC, INFOCOM |
| Analog OTA + non-IID | §V | ✅ Yes | ⚠️ Needs PHY knowledge | High | IEEE JSAC, TWC |
| Async FL bias from fast clients | §VII-3 | ✅ Yes | ✅ Strong (bandit framing) | Medium | NeurIPS, ICLR |
| Heterogeneous model KD wireless | §VII-4 | ✅ Yes | ✅ Good (model efficiency background) | Medium-High | IEEE TNNLS, TWC |
| Scalability to millions | §VII-5 | ✅ Yes | ✅ Strong (proxy idea) | High | MLSys, INFOCOM |

**My recommendation for you specifically:** Direction 3 (async bias-aware bandit scheduling) and Direction 1 (noise+dropout tolerance) are the most tractable for a first MASc paper because they build directly on your existing HeteRo-Select work without requiring deep PHY layer expertise. Direction 5 (scalability via proxies) is your bigger, longer-term thesis arc. Start with 1 or 3, use the proxy idea as your culminating contribution.