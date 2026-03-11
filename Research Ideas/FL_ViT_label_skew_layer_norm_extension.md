# Research Idea: Extending Layer-Norm / Label-Skew FL to Vision Transformers (ViT)

**Source:** Discussion while reading Zhang et al., "Understanding the Role of Layer Normalization in Label-Skewed Federated Learning" (TMLR 2024).  
**Related notes:** `notes/02_Layer_Normalization_Label_Shift_FL.md`

---

## The gap

- **ResNet + LN/FN** under **extreme label skew** (e.g. one class per client, speaker-ID–like) works well: the paper shows that (feature) normalization controls feature collapse and local overfitting, and the theory (Propositions 1 & 2) reduces LN/FN to last-layer normalization under scale equivariance.
- **Vision Transformers (ViT)** are *not* covered: the paper states that "our conclusions do not immediately extend to Vision Transformers, since the multi-head attention is not scale equivariant due to the use of softmax."
- So we have a **theory–practice gap**: FL under label skew is well understood for ResNet; in practice, many systems use ViT. Carrying the story to ViT would close that gap.

---

## The idea

**Introduce a fix or extension for attention / ViT** so that:

1. Either attention (or a variant) becomes **scale equivariant** (or has an analogous property), **or**
2. We get an **LN-like mechanism** for ViT under **extreme label skew** that recovers similar benefits (control feature collapse, reduce local overfitting, accelerate training of feature embeddings).

Such a fix would be a **good addition to both**:

- **Federated learning:** So that state-of-the-art vision models (ViT) don’t degrade under the same non-IID / label-skew settings where ResNet+LN already works.
- **ViT:** So that ViT is usable in federated, label-skewed regimes (e.g. medical, on-device, multi-tenant) without losing the benefits of transformers.

---

## Why ViT (and why it matters)

- **Why use ViT instead of ResNet:** Better accuracy/scaling on large data, backbone of most large vision models (CLIP, DINO, MAE), self-attention gives global context. In practice, the question is often "how do we make FL work with ViT?" not "should we use ViT or ResNet?"
- **Why transform FL into ViT:** If FL methods and theory stay on ResNet, then in real systems (which use ViT) we don’t have the same guarantees or tricks. So these ideas need to be carried over to the ViT regime—either by extending the theory to attention or by showing what does work for ViT under label skew and why.

---

## Literature note

- Work exists on **ViT + federated + label skew** (FedCPD, FedLC, FedVLS, ViT + focal loss, etc.) using **logits calibration, proxy regularization, focal loss, distillation**—not an extension of the LN/FN *theory* (scale equivariance, last-layer normalization) to ViT.
- No paper found that specifically addresses **extreme label skew** (one-class-per-client, speaker-like) for **Vision Transformers** with an LN/FN-style mechanism or a proof analogue of Propositions 1 & 2 for attention.

**Conclusion:** A fix that brings LN-like (or analogous) benefits under extreme label skew to attention/ViT would strengthen both FL and ViT; transforming existing FL results into ViT is important so that FL works in the systems that actually use ViT.

---

## Possible directions (to explore)

- Design an **attention variant** that is scale equivariant (e.g. replace or modify softmax so that scaling inputs scales outputs in a controlled way), then apply the same “reduced to last-layer normalization” argument.
- Propose a **ViT-specific normalization** or architecture change that recovers similar benefits under extreme label skew (control norms, avoid feature collapse) and prove or empirically show it.
- **Empirically** study extreme label skew (one-class-per-client) for ViT in FL and compare with ResNet+LN; then use that to guide a theoretical or architectural fix.

---

## Related idea: Better loss functions for FL (don’t let the pathology happen)

**Source:** Same paper + discussion — cross-entropy minimization can drive $(w_c - w_k)^\top g_\theta(x) \to -\infty$ by blowing up norms, causing feature collapse and local overfitting. The paper fixes this by **normalization** (constrain the representation). An alternative or complement is to fix the **objective**.

**The idea:** Design or use **loss functions (or objectives)** for federated learning that **don’t allow** this pathology — e.g.:

- **Regularization:** Penalize $\|g_\theta\|^2$ or $\|W\|^2$ (or distance to global model, as FedProx) so the loss itself discourages norm blow-up.
- **Label smoothing:** Cap how confident the loss wants the model to be, so pushing logits to $\pm\infty$ is no longer optimal.
- **Calibration / margin:** Losses or logit adjustments that account for local label distribution (FedLC, FedRS) — though they often don’t suffice in **extreme** one-class; a loss designed explicitly to prevent collapse might help.
- **Explicit anti-collapse term:** E.g. FedDecorr-style term that penalizes correlated dimensions; or a term that penalizes large feature norms when they’re used only to satisfy the classifier.

**Why it matters:** Normalization and “better loss” are **complementary**. The paper keeps the loss and constrains the model; the other direction is to keep the model and constrain or change the loss so the same pathology doesn’t occur. Both can be combined (e.g. LN + regularized loss, or LN + label smoothing). Good direction for FL under label skew: either theoretical (when does a given loss avoid collapse?) or empirical (loss + LN vs LN alone in extreme skew).

---

*Idea saved for revisiting. Link to paper: `mehdi papers/02_Layer_Normalization_Label_Shift_FL_MUST_READ.pdf`.*
