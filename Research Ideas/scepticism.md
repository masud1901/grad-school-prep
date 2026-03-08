Great question. There were several waves of skepticism about deep learning, each broken by specific papers.

## Wave 1 — "Neural networks can't learn properly" (1980s-90s)

**Skepticism:**
> *"Backpropagation doesn't work in deep networks — gradients vanish before reaching early layers"*

The math seemed to confirm it. Gradients multiply through layers:

```
Layer 1 ← Layer 2 ← Layer 3 ← Layer 4 ← Layer 5

gradient × 0.1 × 0.1 × 0.1 × 0.1 = 0.0001
                                      ↑
                               essentially zero
                               early layers don't learn
```

So the consensus was — shallow networks only, 2-3 layers max.

**Papers that broke it:**
- **Hochreiter & Schmidhuber 1997** — LSTM, solved vanishing gradients for sequences by introducing gates that let gradients flow unchanged
- **Hinton et al. 2006** — "A Fast Learning Algorithm for Deep Belief Nets" — showed you could pretrain layers greedily, then fine tune. Reignited deep learning entirely
- **Glorot & Bengio 2010** — showed the problem was partly weight initialization, proposed Xavier initialization
- **He et al. 2015** — ReLU + proper initialization basically killed vanishing gradients for feedforward networks

---

## Wave 2 — "Neural networks can't beat classical ML" (2000s)

**Skepticism:**
> *"SVMs and kernel methods are theoretically grounded and beat neural networks on every benchmark. Why bother with this black box?"*

SVMs had:
- Convex optimization — guaranteed global optimum
- Statistical learning theory — generalization bounds
- Kernel trick — implicit infinite dimensional features

Neural networks had:
- Non-convex loss landscape
- No theoretical guarantees
- Slow to train
- Needed lots of data

The ML community largely abandoned neural networks for SVMs and boosting methods like AdaBoost.

**Paper that broke it:**
- **Krizhevsky, Sutskever & Hinton 2012** — **AlexNet**

This was the earthquake. On ImageNet:

```
Classical computer vision (2011):  ~26% error
AlexNet (2012):                    ~15% error
                                    ↑
                               10% gap overnight
                               nobody saw it coming
```

AlexNet combined:
- Deep CNN architecture
- ReLU activations
- Dropout regularization
- GPU training

After this paper, the entire ML community pivoted to deep learning almost overnight.

---

## Wave 3 — "Deep networks are just memorizing" (2010s)

**Skepticism:**
> *"These networks have more parameters than training examples — they're just memorizing data, not learning generalizable patterns"*

Classical statistics said this clearly — overparameterized models **should** overfit. Bias-variance tradeoff dictated:

```
More parameters → lower bias, higher variance → overfit
```

And yet deep networks were generalizing beautifully despite being massively overparameterized.

**Paper that broke it:**
- **Zhang et al. 2017** — "Understanding Deep Learning Requires Rethinking Generalization"

They showed neural networks could **perfectly memorize random labels** on CIFAR-10 — proving networks have enough capacity to just memorize. Yet on real labels they generalize. Classical theory couldn't explain this at all.

Then:
- **Belkin et al. 2019** — "Reconciling modern machine learning practice and the bias-variance tradeoff"

Introduced the **double descent curve**:

```
Test error

    ╲                    ╱╲
     ╲                  ╱  ╲          ╲
      ╲                ╱    ╲          ╲___
       ╲______________╱      ╲
                              ↑
classical regime         interpolation    overparameterized
bias-variance            threshold        regime
tradeoff holds           (overfitting)    error drops again!
```

Beyond a certain model size, more parameters actually **helps** generalization. Classical theory completely breaks down here.

---

## Wave 4 — "You can't train very deep networks" (2014-15)

**Skepticism:**
> *"Beyond ~20 layers, networks get harder to train — deeper isn't always better"*

Experimentally verified — just stacking more layers made training worse, even with ReLU and good initialization.

**Paper that broke it:**
- **He et al. 2015** — **ResNet** "Deep Residual Learning for Image Recognition"

The insight was simple but profound — instead of learning the mapping $H(x)$, learn the **residual** $F(x) = H(x) - x$:

```
Without skip connections:        With skip connections (ResNet):

x → [layer] → [layer] → y       x → [layer] → [layer] → + → y
                                 │                         ↑
                                 └─────────────────────────┘
                                          skip connection
                                 gradient highway — flows freely
```

This allowed training of 152 layer networks. Won ImageNet 2015 by a massive margin.

---

## Wave 5 — "Attention and transformers can't scale" (2017-2020)

**Skepticism:**
> *"Transformers are too expensive — attention is quadratic in sequence length, they'll never scale to real problems"*

**Papers that broke it:**
- **Vaswani et al. 2017** — "Attention is All You Need" — introduced transformers
- **Brown et al. 2020** — **GPT-3** — showed scaling laws, bigger = better in a predictable way
- **Kaplan et al. 2020** — "Scaling Laws for Neural Language Models" — showed loss follows a **power law** with compute, data, and parameters

```
Loss

  ╲
   ╲
    ╲
     ╲___________
                  → scale (compute/data/params)

predictable improvement
just keep scaling
```

This paper essentially gave theoretical justification for just throwing more compute at the problem.

---

## The Meta Pattern

Every wave follows the same structure:

```
Theoretical/empirical skepticism
        │
        ▼
"It can't work because [principled reason]"
        │
        ▼
Someone ignores the theory and tries anyway
        │
        ▼
Empirical result destroys the skepticism
        │
        ▼
Theory catches up years later (sometimes)
```

> **Deep learning's history is essentially empiricism repeatedly beating theory.** The practice kept working before anyone could explain why — and in many cases the theoretical explanation still isn't fully there.

That's what makes it simultaneously exciting and unsatisfying as a field.