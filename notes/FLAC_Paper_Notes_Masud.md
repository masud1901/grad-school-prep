## FLAC (Federated Learning with Autoencoder Compression) – Notes

**Citation**: M. Beitollahi and N. Lu, “FLAC: Federated Learning with Autoencoder Compression and Convergence Guarantee,” IEEE GLOBECOM 2022.

### 1. Core Idea

- **Problem**: In FL, uplink transmission of large models from clients to server is the main bottleneck (e.g., VGG with 138M params). Need to reduce communication without destroying convergence.
- **Key mechanism**: Learn a **single autoencoder** at the server that compresses client models into a low-dimensional latent vector for uplink; server decodes and aggregates.
  - Encoder: w_i_en[t] = f_en(w_i[t]) with |w_en| << |w|.
  - Decoder: w_i_de[t] = f_de(w_i_en[t]).
  - Aggregation uses decoded models: w_hat[t+1] = (1 / sum_i D_i) * sum_i D_i * w_i_de[t].
- **Lossy compression**: Autoencoder is trained with L2 reconstruction loss ||w_i - w_i_de||_2^2; decoding is approximate, so there is an error e_i[t] = w_i[t] - w_i_de[t].

### 2. Dynamic FLAC Mechanism

- **Two states**:
  - **Training State**: No compression. For q global rounds, clients send full models; server trains/validates/tests the autoencoder on these models.
  - **Compression State**: Clients use encoder to send w_i_en[t]; server decodes and aggregates.
- **Error control**:
  - Windowed estimates of mean and variance of e_i[t] over last q rounds.
  - Target: satisfy conditions (9) from paper:
    - E[||e_i[t]||_2^2] <= e_th[t] <= eta_t^2 = O(1 / t^2).
    - E[e_i[t]] = 0.
  - If conditions fail in Training State → retrain autoencoder, **decrease compression rate** C (i.e., increase |w_en|) and/or **add accurate users**.
  - If conditions fail in Compression State → switch back to Training State and re-train from scratch.
- **Accurate users**:
  - Subset AC ⊆ K of users upload uncompressed models.
  - Decoder input is concatenation (w_i_en[t], w_ac[t]); accurate users help reconstruction of compressed users.
  - Global aggregation mixes decoded compressed users and exact accurate users.

### 3. Theoretical Guarantee (Theorem IV.1)

- Assumes standard convex optimization conditions on global loss F(w):
  - **L-smooth** and **mu-strongly convex**.
  - **Bounded stochastic gradients** and variance.
  - Equal local dataset sizes and full participation.
- Uses result from [15]: FedAvg with noisy uplink/downlink still converges at O(1 / T) if uplink error is **zero-mean** and variance decays as O(1 / t^2).
- FLAC enforces this by:
  - Choosing learning rate eta_t = 2 / (mu * (gamma + t)).
  - Forcing autoencoder error threshold e_th[t] <= eta_t^2 = O(1 / t^2) and E[e_i] = 0.
- Result: E[F(w_hat[T])] - F(w*) <= O(1 / T), **same rate as FedAvg without compression**, with an extra constant factor capturing compression noise.

### 4. Computational and Communication Trade-offs

- **Compression rate**: C = |w| / |w_en|.
  - Larger C → smaller latent dimension → higher compression but larger reconstruction error.
- **Extra FLOPs**:
  - Encoder (client): linear layer |w| × |w_en|.
  - Decoder (server, with accurate users): (|w_en| + |w_ac|) × |w|.
  - Total additional cost: |w| * |w_en| + (|w_en| + |w_ac|) * |w| FLOPs.
- **Communication pattern**:
  - **Uplink**: large savings (clients send |w_en| instead of |w| per round).
  - **Downlink**: extra cost to distribute autoencoder parameters when entering Compression State, but downlink is usually cheaper than uplink in wireless systems.

### 5. Experimental Findings (MNIST, CIFAR-10, Speech Commands)

- Setup: 10 users, non-i.i.d. data, CNN models as in Flower FLAC codebase.
- Baselines:
  - FedAvg without compression.
  - Fixed autoencoder with constant compression (no dynamic state switching).
- Results:
  - A **fixed autoencoder** significantly degrades accuracy over time (its error becomes too large as FL converges).
  - **FLAC** achieves:
    - MNIST: compression ≈ 875× with ~3% accuracy gap vs. no compression.
    - CIFAR-10: compression ≈ 83× while staying within ~7% of non-compressed accuracy.
    - Speech dataset: compression ≈ 133× with ~7% gap.
  - Plots show FLAC’s accuracy trajectories closely track “Without Compression,” with blue markers at rounds where state switches occur.

### 6. Takeaways for ADAFL / Future Work

- FLAC is an example of **learned, non-fixed compression** in FL with:
  - Explicit control over compression error vs. convergence, grounded in theory.
  - Dynamic switching logic that adapts compression rate over time.
- For ADAFL:
  - FLAC helps motivate that **controlled noise** (bounded, decaying) in uplink updates is acceptable.
  - Shows how to design a practical algorithm that enforces theoretical error conditions via simple state machines (Training vs. Compression) and tunable knobs (compression rate, accurate users).

