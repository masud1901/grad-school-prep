# Reading Notes: Layer Normalization in Label-Skewed Federated Learning

**Paper:** Understanding the Role of Layer Normalization in Label-Skewed Federated Learning  
**Authors:** Zhang, Beitollahi, Bie, Chen (Huawei Noah's Ark Lab)  
**Source:** `mehdi papers/02_Layer_Normalization_Label_Shift_FL_MUST_READ.pdf`

---

## 1. Paper summary (what it's about)

- **Question:** Why does **layer normalization (LN)** help so much in **federated learning (FL)** when data is **non-i.i.d.** due to **label shift**?
- **Label shift:** Different clients have different label distributions (e.g. one client mostly "cat", another mostly "dog"). **Extreme:** some clients see only **one class**.
- **Main finding:** LN (and a simpler variant, **feature normalization FN**) greatly improve FL under label shift. The key mechanism is **FN**: normalize the **last-layer features** before the classifier. This reduces **feature collapse** and **local overfitting** (clients can't "cheat" by blowing up norms).
- **Expressive power:** LN/FN do **not** add expressive power; a vanilla network can represent the same functions. The gain is in **training dynamics**, not model class.
- **When it helps:** The benefit is tied to label shift; when data is more i.i.d., the gap shrinks.

---

## 2. How to read the preliminaries (FL objective & notation)

### Global objective

$$
f(\theta, W) = \sum_{k=1}^{K} \frac{m_k}{m} f_k(\theta, W)
$$

- $K$ = number of clients  
- $m_k$ = number of samples at client $k$, $m = \sum_k m_k$  
- Minimizing global loss = minimizing a **weighted average** of per-client losses.

### Client loss (Equation 1)

$$
f_k(\theta, W) := \mathbb{E}_{(x,y)\sim D_k}[\ell(\theta, W; x, y)]
$$

- $D_k$ = data distribution of client $k$ ( **any** distribution, not necessarily normal).  
- In practice we approximate with the average over client $k$'s dataset.

### Per-sample loss (Equation 2)

$$
\ell(\theta, W; x, y) = -\log \frac{\exp(w_y^\top g_\theta(x))}{\sum_{c\in[C]} \exp(w_c^\top g_\theta(x))}
$$

- $g_\theta(x)$ = feature vector; $W = (w_1,\ldots,w_C)$ = classifier weights.  
- This is **cross-entropy** for one example $(x,y)$.

### Parameters
- $\theta$: feature network $g_\theta$  
- $W$: classifier (one $w_c$ per class).  
FN normalizes $g_\theta(x)$ before it goes into the classifier.

### FedAvg protocol (one sentence)
Server sends model → clients do local gradient steps → clients upload models → server **aggregates** (e.g. averages) → repeat.

---

## 3. Expectation over $D_k$

- **$D_k$:** Not assumed normal/Gaussian. It can be **any** distribution describing client $k$'s data.
- **$\mathbb{E}_{(x,y)\sim D_k}[\ell(\theta,W;x,y)]$** means:  
  **Average loss** of the model $(\theta,W)$ when $(x,y)$ is drawn from $D_k$.  
  In other words: "How well does the model do on average on client $k$'s data?"

---

## 3b. MV normalization: BN vs LN (which dimension?)

- **MV normalization** = mean–variance normalize (subtract mean, divide by std). We can apply it to **tensors** by choosing **which dimension** to take mean/variance over.
- **Batch normalization (BN):** MV normalization **along the batch/samples dimension**. So each channel (or neuron) is normalized across the batch. Needs many samples per batch; statistics depend on who is in the batch → problematic in FL when clients have different data.
- **Layer normalization (LN):** MV normalization **along the hidden-neurons (feature) dimension**. So each *sample* is normalized across its own features. No dependence on batch size; each client can normalize on its own activations → works well in FL.
- **One-line:** BN = normalize along samples; LN = normalize along hidden neurons. LN is "per sample" and doesn't need batch statistics, which is why it fits federated learning.

---

## 4. Affine vs linear (formal)

- **Affine:** $A_i(a) = U_i a + b_i$ → **linear part** ($U_i a$) **+ translation** ($b_i$).  
- **Linear:** Special case $b_i = 0$, i.e. $A_i(a) = U_i a$.
- So: **affine = linear + bias**. Affine is the bigger family; linear is a special case.
- In the paper, **Assumption 1** sets $b_i = 0$ for $i \geq 2$ so that from layer 2 onward the map is linear → gives **scale equivariance** used in the propositions.

---

## 5. Special maths with affine: lifting to higher dimension

- We can **embed** an affine map in $\mathbb{R}^n$ into a **linear** map in $\mathbb{R}^{n+1}$:

$$
\widetilde{x} = \begin{bmatrix} x \\ 1 \end{bmatrix}, \quad
\widetilde{T}(\widetilde{x}) = \begin{bmatrix} U & b \\ 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ 1 \end{bmatrix} = \begin{bmatrix} Ux + b \\ 1 \end{bmatrix}.
$$

- In the higher dimension, the **same** transformation is **linear**. We can use eigenvalues, determinants, composition, etc. The original behaviour is **preserved** (no information lost).
- **Determinant:** $\det \begin{bmatrix} U & b \\ 0 & 1 \end{bmatrix} = \det(U)$.  
- **Eigenvalues:** Those of $U$ plus an extra 1 from the last row.

---

## 6. Big idea: "squiggly" → linear in higher dimension

- **Pattern:** In the original space something may look "squiggly" (e.g. affine, nonlinear). In a **higher dimension** we can **embed** it so that the **same** situation is described by a **linear** map.
- Then we apply **known linear maths** (eigenvalues, determinants, inversion, etc.) **without breaking the link** to the original problem. Conclusions in the higher dimension translate back exactly.
- **Examples:**  
  - Affine in $\mathbb{R}^n$ → linear in $\mathbb{R}^{n+1}$ (lift with 1).  
  - Kernel methods: data in $\mathbb{R}^d$ → map to higher (or infinite) dimensional feature space where the problem becomes linear (e.g. linearly separable).
- **One-line summary:** Embed the original setup into a higher dimension where it becomes linear, apply well-known linear algebra there, and interpret results back in the original setting without changing the underlying relationships.

---

## 7. Why neural nets work (upstream / downstream)

- **Upstream:** Map inputs into a (often high-dimensional) space where the task is **linear enough** — i.e. the layers learn a representation $g_\theta(x)$ so that in that space the problem is linearly separable (classification) or approximately linear (regression).
- **Downstream:** Do a simple **linear** model (logistic regression or softmax) on that representation; the **mode** of that distribution is the prediction.
- So: NNs work by learning a space where things are linear enough, then we do regression/softmax there and the mode (or linear output) is the answer. The "magic" is in the upstream; the downstream is intentionally simple (linear + softmax).

---

## 8. $p(y=c \mid x)$ and the likelihood (Bayesian)

- **$p(y=c \mid x)$** = conditional probability of class $c$ given input $x$ (the distribution we model, e.g. with softmax).
- **Likelihood** (for one sample) = probability of the **observed** label given input and parameters, as a function of $\theta$: $L(\theta) = p(y \mid x, \theta)$.
- So the **model** gives $p(y=c \mid x, \theta)$. For the observed $(x,y)$, $p(y \mid x, \theta)$ is exactly the likelihood for that sample. Same object: the softmax probability for the true class is the likelihood (for that example).

---

## 9. MAP and backprop: "go back" with calculus to update all the Ws

- **MAP** = Maximum A Posteriori = $\theta^* = \arg\max_\theta \, p(\theta \mid \text{data}) = \arg\max_\theta \, p(\text{data} \mid \theta)\, p(\theta)$.
- We don't find MAP in one shot. We **iteratively** minimize **negative log posterior**:
  $$
  -\log p(\theta \mid \text{data}) = -\log p(\text{data} \mid \theta) - \log p(\theta) = \text{loss} + \text{regularizer}.
  $$
- **"Go back" with calculus** = **backpropagation**: use the **chain rule** to compute $\frac{\partial L}{\partial W}$ for every weight $W$ in the network, layer by layer from output to input.
- **Update:** $W \leftarrow W - \text{learning rate} \times \frac{\partial L}{\partial W}$ for all Ws.
- **One-line:** We iteratively approximate MAP by minimizing loss + regularizer; we "go back" with the chain rule (backprop) to get gradients for every $W$, then update all Ws.

---

## 10. How the paper supports its three claims (Why is LN so helpful?)

| Claim | How they support it |
|-------|---------------------|
| **(a) LN/FN = last-layer normalization** | **Propositions 1 & 2**: Under Assumption 1 (ReLU, zero bias from layer 2), scale-normalizing every layer (FN) ≡ only last-layer scale; MV-normalizing every layer (LN) ≡ only last-layer MV + mean-shift $s$ in middle layers. Proofs in appendix. |
| **(b) Helps feature collapse, accelerates training** | **Theorem 1**: One-class local loss forces (weight or feature) norms to diverge. **Figures 2 & 3**: Vanilla → feature collapse (one dominant singular value); FN → more spread. Constraining norms forces learning directions. |
| **(c) Benefit in training, not expressive power** | **Proposition 3**: Vanilla and FN/LN have the **same** 0–1 error (same function class). So gains must come from **optimization/training**, not from representing more functions. |

---

## 11. Why "only last layer" is enough (reading the reduction)

- **Scale equivariance:** With ReLU and zero bias from layer 2 onward, scaling an intermediate activation by $\lambda > 0$ just scales the next activations by $\lambda$. So the block is scale equivariant: only **direction** matters, not scale.
- **What $n$ does:** Scale normalization $n(x) = x/\|x\|$ keeps direction, throws away scale.
- **So:** Intermediate normalizations only remove scale that would have propagated as one factor; only the **last** normalization fixes the final direction. So doing $n$ at every layer gives the **same** final output as doing $n$ only at the last layer — same direction. Prop 1 proof: $a_i = \lambda_i a'_i$; at last layer $\lambda$ cancels in the ratio ⇒ $a_L = a'_L$.

---

## 12. Prop 1 vs Prop 2 (FN vs LN reduction)

- **Prop 1 (FN):** Scale-normalizing **every** layer ≡ **only** scale-normalizing the **last** layer. So for FN we can drop $n$ in the middle; only last-layer scale norm remains.
- **Prop 2 (LN):** MV-normalizing every layer ≡ **only** MV-normalizing the **last** layer and applying **mean-shift** $s(x) = x - \mu(x)\mathbf{1}$ in previous layers. So for LN the **mean has to be subtracted** in the middle (that's $s$); only the last layer gets full $n_{MV}$. Proof follows analogously to Prop 1.

**Table 3 — what actually proves the propositions (empirically):** The paper compares **layer-wise** vs **last-layer-only** normalization under label skew (1 class, 2 classes, Dir(0.1)), with 10 clients. Notation: $n^L$ = layer-wise scaling (FedFN); $n_1^{L-1}$ = last-layer scaling; $n_{MV}^L$ = layer-wise MV (FedLN); $n_{MVS}^{L-1}$ = last-layer MV. **FedFN** $n^L$ vs $n_1^{L-1}$ and **FedLN** $n_{MV}^L$ vs $n_{MVS}^{L-1}$ give **very similar accuracies** in each setting (e.g. ~77% vs ~77% for 1-class). So doing normalization everywhere vs only at the last layer yields nearly the same performance — **Table 3 is the empirical validation** of Prop 1 and Prop 2. The table also shows **with vs without bias** (Assumption 1): performance is similar in both cases, so the zero-bias assumption does not change the practical conclusion.

---

## 13. ResNet extension, $\lambda$, and ViT/attention

- **$\lambda$ (lambda):** A **scalar scaling factor**. **Scale equivariance** means: for any $\lambda > 0$, block$(\lambda x) = \lambda \,$block$(x)$. So scaling input scales output by the same factor.
- **ResNet:** The paper extends Prop 1 & 2 to ResNet: each block is scale equivariant, so a similar "reduced" form holds (last-layer LN, mean-shift $s$ in the residual path where possible). **Caveat:** The second $n_{MV}$ inside the block cannot be replaced by $s$ → larger gap between LN and "only last-layer feature norm" for ResNet (Table 2).
- **ViT / attention:** "Our conclusions do **not** immediately extend to Vision Transformers, since the **multi-head attention is not scale equivariant** due to the use of **softmax**." So the LN/FN reduction story does not apply to ViT as-is. (See Research Ideas: extending this to ViT.)

---

## 14. Expressive power: ruling out "better functions"

- **Two possible reasons** LN helps: (1) LN expresses **better functions** to fit the data, or (2) LN makes the **training process** faster/better.
- **Prop 1 & 2** show LN/FN = last-layer normalization (same output as a network that only normalizes at the end). So we're not adding new *structure* — same function, just normalized at the end.
- **Prop 3** says: for any LN/FN network there exists a **vanilla** network with the **same** 0–1 error on all $(x,y)$. So the **set of decision boundaries** we can get is **not larger** with LN/FN.
- **Conclusion:** We **rule out** the first explanation. LN does **not** introduce new or better functions. The benefit lies in the **training process** (avoiding collapse, preserving global knowledge, faster convergence). **No new functions — just a fix that makes what was already there better.**

---

## 15. Figure 2: LN makes the client know other classes better

- **Setting:** One-class per client (e.g. client has only class 0). After 5 local steps:
- **Vanilla:** Client gets ~100% on class 0 but **collapses on other classes** (accuracies near 0%) — severe local overfitting, "forgets" other classes.
- **FN/LN:** Client still does well on class 0 (~95–100%) and **retains much higher accuracy on other classes** (e.g. 20–60%). So the client **keeps knowing other classes** — doesn't wipe out the global model's knowledge.
- **Mechanism:** LN/FN constrain updates so the client can't "cheat" by blowing up norms to fit only its one class; the **information that was in the server model** (knowledge of other classes) is **preserved** during local training. So LN helps the client **retain** what the server gave — no new functions introduced, just the same function class trained and preserved better.

---

## 16. Blocks (attention, ResNet, VGG) are not affine — and they have to be

- **Single linear/conv layer** (no activation) = affine ($Wx + b$). **ReLU, softmax, max pool** = nonlinear.
- **VGG block, ResNet block, attention** as a whole are **not** affine: they contain ReLU, softmax, etc. Only their **building blocks** (linear/conv layers) are affine.
- **Why they have to be non-affine:** Composing affine maps is still affine → a network that was all affine would be **equivalent to one affine map** = no more expressive than linear regression. So we'd achieve nothing. The blocks have to be **nonlinear** so the full model can do more than a single linear map — they take the same kind of data, can reduce space/compute, keep useful structure, but must be non-affine or there'd be no point to the architecture.

---

## 17. Upper limit: maximally compressed, minimally degrading block

- **Question:** Is there an upper limit to how "maximally compressed and minimally degrading" a block can be?
- **Yes, in theory:**  
  - **Rate–distortion:** For a given allowed distortion $D$, there is a **minimum rate** $R(D)$ (compression) needed. So for a given degradation, there's a limit to how much we can compress.  
  - **Information bottleneck:** Optimal tradeoff between compressing $I(X;Z)$ and preserving task-relevant info $I(Z;Y)$. The best block would sit on this curve.  
  - **Minimal sufficient statistic:** For a fixed task, there is a maximally compressed summary that loses **no** information about the target — that's the limit of "zero degradation, maximum compression."
- **In practice:** We usually **can't write down** that optimal structure in closed form. We approximate it with hand-designed or searched architectures (conv, attention, etc.). So the limit **exists** theoretically; the "maximally compressed, minimally degrading" block would attain it, but we don't know its exact form.

---

## Quick reference

| Term | Meaning |
|------|--------|
| **Logit** | The **pre-softmax** score for a class. For class $c$, logit = $z_c = w_c^\top g_\theta(x)$ (or in general, the input to softmax). Softmax turns logits into probabilities: $p(y=c\mid x) = e^{z_c} / \sum_j e^{z_j}$. So **logit** = unnormalized score / "log-odds" style input to the softmax. |
| LN | Layer normalization (mean–variance norm per layer) |
| FN | Feature normalization (normalize last-layer features before classifier) |
| Label shift | Clients have different label distributions |
| Feature collapse | Embeddings collapse to few directions (one dominant singular value) |
| Local overfitting | Model fits local data perfectly but fails on other clients |
| FedLN / FedFN | FedAvg + LN / FedAvg + FN |
| **Centralized training** | All data pooled in one place; one model trained on the full dataset (no federation). Used as **upper benchmark**: how well you could do without FL constraints. Figure 4 compares FedAvg/FedFN/FedLN to this. |

---

## Likelihood / predicted distribution collapse under extreme label shift

- Under **extreme label shift** (e.g. one class per client), **softmax by nature** pushes the model to become very confident: it minimizes cross-entropy by making the correct-class logit much larger than others ($z_k \to +\infty$, $z_c \to -\infty$ for $c \neq k$).
- So the model's **predicted distribution** over labels, $p_\theta(y\mid x)$, collapses to a **point mass** on one class: probabilities go to $(0,\ldots,0,1,0,\ldots,0)$. So the **variance of that distribution goes to zero** — the "likelihood part" (the spread over classes) gets **demolished**; the model can't represent uncertainty or other classes.
- **Dual view:** **Logits** (the $z_c$'s) spread apart → their range goes to infinity. **Probabilities** collapse → variance goes to zero. So "variance → zero" is the right description for the **predicted label distribution** $p_\theta(y\mid x)$.
- **FN/LN** prevent this by constraining norms so logits can't blow up; the predicted distribution stays spread (nonzero variance) and the model can still "understand" other classes.

---

## What LN/FN bound — and why that's enough for good results

- **LN/FN only bound the feature $g_\theta(x)$.** They are applied to the last-layer representation before the classifier. The classifier weights $w_c$, $w_k$ are **not** normalized by LN/FN, so $\|w_c\|$ and $\|w_k\|$ are **not** directly affected. Theoretically they could still explode (then logits $z_c = w_c^\top \tilde{g}$ could still blow up). So LN/FN **do not** by themselves bound the "overall" logits; they only remove **one** of the three ways (feature norm) in Theorem 1.
- **Why we still get good results (~80%, FedLN/FedFN):** By bounding $g_\theta(x)$, the model **can't** minimize the loss by quickly blowing up feature norms — the "easy" shortcut is closed. So the **other norms** ($w_c$, $w_k$) don't explode quickly either; the optimizer is forced to **learn** (improve directions, use server signal, build useful features) instead of just scaling. That real learning is why we get the good accuracy. Any collapse that happens **later** does so on a model that's already much better (global representation and aggregation have improved), so things stay better overall. **Summary:** Bounding $g$ → other norms don't explode fast enough → learning has to happen → good results; later collapse (if any) happens on a better model.

---

## Experimental evidence: which norms diverge, and feature collapse (Figure 3)

### Which norms diverge?

- The paper (Figure 3) runs **local training on a client that has only one class** (e.g. class 1 of CIFAR-10) to see which norms blow up.
- **Left panel:** Norms of classifier weights $\|w_c\|$ for all classes $c \in [10]$. The weight for the **present** class, $\|w_1\|$, is relatively large compared to others — the model pushes the decision boundary toward the class it sees.
- **Middle-left panel:** For 20 images from that same client, the **feature norms** $\|g_\theta(x)\|$ are computed. They are **all very large** compared to class embeddings.
- **Takeaway:** Both classifier norms and feature norms can get huge. Together with Theorem 1 and the figure, the paper argues that **controlling the divergence of feature norms is more important** — i.e. bounding $g_\theta(x)$ (what LN/FN do) is the right lever. So the experiments back the idea that focusing on feature norm is what matters most.

### Feature collapse explains local overfitting

- To see whether the feature extractor **separates** different classes, the authors form a **feature matrix** from 20 images from **different** classes: $[g_\theta(x_1), \ldots, g_\theta(x_{20})]$, then look at its **singular values**.
- They observe a **huge spectral gap:** $\sigma_1 \approx 1.9 \times 10^3$, $\sigma_2 \approx 6.9$. So the first singular value dominates; the rest are much smaller.
- **Interpretation:** That gap means the feature embeddings lie **approximately in a one-dimensional subspace**. Most of the variance is in a single direction; other directions carry very little information.
- **Feature collapse** (cf. Shi et al. 2023): Different classes get mapped to **the same directions**. The network $g_\theta$ outputs features that are almost **collinear** across classes — so the classifier cannot tell them apart.
- **Why this explains local overfitting:** On a one-class client, the model learns to push features to huge magnitude along one direction. When data from **other** classes appears (e.g. at test or on other clients), it too gets mapped into that same dominant direction. The classifier has no discriminative signal, so it fails to generalize — **local overfitting**. So feature collapse is the *mechanism*: collapse of embeddings to one subspace → no discriminative directions → predicted distribution collapse and poor accuracy on unseen classes.

---

## Figure 4: FL vs centralized under one-class label shift

- **Plot:** Test accuracy (%) vs number of batches (0–40k) on CIFAR-10 with **one-class label shift**.
- **Centralized – Vanilla:** All data in one place, one model, standard training. Reaches **~78%** by 40k batches — the **upper benchmark** FL aims toward.
- **FedAvg – Vanilla:** Worst; starts ~10%, ends **~42%** — big gap from centralized.
- **FedFN / FedLN:** Start ~10–12%, rise quickly, end **~70–72%**. Much closer to centralized than FedAvg-Vanilla; FedLN briefly matches or beats centralized early on.
- **Takeaway:** LN/FN don’t match centralized but **close most of the gap** that vanilla FedAvg loses under label skew. Simple normalization recovers a large part of the performance you’d get without federation.

---

## Lemma, theorem, proposition — what’s the difference?

- **Theorem:** A main, important result the paper wants to highlight. Usually has a non-trivial proof. (In this paper there is **one** theorem: Theorem 1, divergent norms.)
- **Proposition:** A stated result that is also proved, often a bit more technical or a stepping stone. (Here: Prop 1 and 2 = reduction to last-layer norm; Prop 3 = expressive power.)
- **Lemma:** A smaller, auxiliary result used mainly to prove a theorem or proposition. (Here: Lemma 1 in the appendix, e.g. scale-equivariant activation.)

So: **lemma** = helper; **proposition** = stated result (often a step); **theorem** = main result. Naming is convention, not a strict rule.

---

## Theorem 1 (divergent norms): proof breakdown

**Statement:** To minimize the one-class local loss $f_k(\theta, W)$, at least one of: (1) $\|w_c\| \to \infty$ for all $c \neq k$, (2) $\|g_\theta(x_i)\| \to \infty$ for all $x_i$, (3) $\|w_k\| \to \infty$.

**Key condition:** $(w_c - w_k)^\top g_\theta(x_i) < 0$ for all $x_i \in S_k$ and $c \neq k$ (correct class has largest logit).

**Part 1 — Possibility:** Construct a network where $g_\theta(x_i) = \rho(x_0)\mathbf{1}$ for all $x_i$, set $w_k = \rho(x_0)\mathbf{1}$, $w_c = -\rho(x_0)\mathbf{1}$ for $c \neq k$; then the condition holds. Replace $W$ by $tW$ and let $t \to \infty$: loss $f_k \to 0$. So the **infimum of $f_k$ is 0**.

**Part 2 — Necessity of the condition:** If $(w_c - w_k)^\top g_\theta(x_i) \geq 0$ for some $x_i, c \neq k$, then $f_k \geq \frac{1}{m_k}\log C > 0$, so loss cannot go to zero. So to minimize toward 0 we **must** have the condition everywhere.

**Part 3 — Necessity of divergence:** Suppose the condition holds but **all three** norm statements are false (all norms bounded). Then for some $x_i$, $w_c$, $w_k$ we have bounded norms, so the per-sample loss $\ell(\theta,W;x_i,k)$ is bounded below, hence $f_k$ is bounded below. Contradiction with Part 1 (we can drive $f_k$ to 0). So when the condition holds, **at least one** of the three norms must diverge.

**Summary:** Possibility (Part 1) + necessity of condition (Part 2) + impossibility of “condition + all norms bounded” (Part 3) ⇒ at least one norm diverges. *(Assumes softmax/cross-entropy loss.)*

---

## 18. Related idea: Better loss functions for FL

- **Paper’s approach:** Keep cross-entropy; **constrain the representation** (LN/FN) so minimizing the same loss doesn’t lead to norm blow-up and feature collapse.
- **Alternative / complement:** Use a **better loss** (or objective) that **doesn’t let** the pathology happen — e.g. regularization (penalize $\|g_\theta\|$, $\|W\|$, or distance to global model), label smoothing (cap confidence), or an explicit anti-collapse term (e.g. FedDecorr). FedLC/FedRS modify logits but often don’t suffice in extreme one-class.
- **Takeaway:** Normalization and “better loss” are **complementary**; both can be combined (e.g. LN + regularized loss). Open direction: design or analyze losses for FL that explicitly prevent collapse / local overfitting under label skew, or combine them with LN/FN.

---

## Takeaways: why the paper works (reading summary)

- **Simple story:** One mechanism (bound feature norms → force real learning), one theoretical simplification (last layer is enough), one pathology (norms diverge / feature collapse). No fancy machinery.
- **Theory ↔ experiments:** Propositions and Theorem 1 are **fully backed** by the experiments: Table 3 (layer-wise vs last-layer), Figure 3 (which norms, feature collapse), Figure 2 (retain other classes), Figure 4 (vs centralized). Each experiment answers a clear "does the theory hold?" question.
- **LN works in both regimes:** Helps in **extreme** label shift (one-class) and in **milder** skew (2 classes, Dir(0.1)). Low computational cost — just normalization, no extra communication or hard-to-tune regularizers.
- **Presentation:** Pacing works: intuition → formal reduction (Prop 1 & 2) → pathology (Thm 1) → experiments that validate each step. One idea carried through from motivation to math to experiments, so nothing feels loose or decorative.

---

## Writing / storytelling takeaways (for future work)

- **Story arc to reuse:** One clear mechanism → one formal simplification → one pathology → experiments that map onto each step. No detours; no "and we also tried X." State the idea early, then theory that makes it precise, then "here's what goes wrong without it," then experiments that directly test those claims.
- **Everything informed:** When the authors say something, it's backed — either by their own proof (Prop 1 & 2, Thm 1), by a figure (Figure 3 for which norms diverge, Table 3 for layer-wise vs last-layer), or by prior work (e.g. feature collapse, Shi et al.). Every claim is either proved, cited, or shown in a table/figure. That's why it feels like they read many papers to get here: they only say what they can back. **Template:** Claim → proof / citation / experiment; no loose statement.
- **Use for own papers:** Follow this storytelling (one idea, theory strips it to essentials, experiments validate each part) and keep every claim informed (proof, cite, or show).

---

*Notes preserved for revisiting. Add new chunks below as you read more.*
