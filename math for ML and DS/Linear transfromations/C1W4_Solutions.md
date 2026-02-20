# C1W4 Assignment Solutions — Eigenvalues & Eigenvectors

All 5 exercises solved. Copy-paste each solution into the corresponding cell in the notebook.

---

## Exercise 1 — Matrix Multiplication (X₁ = P·X₀)

Replace `X1 = None` with:

```python
X1 = P @ X0
```

**Intuition:** Starting at page 4, `X0 = [0,0,0,1,0]`. Multiplying by P picks out the 4th column of P, giving probabilities of landing on each page: `[0.25, 0.25, 0.25, 0, 0.25]`.

---

## Exercise 2 — Verify Eigenvector (P·X_inf = X_inf)

Replace `X_check = None` with:

```python
X_check = P @ X_inf
```

**Intuition:** If X_inf is the eigenvector for eigenvalue 1, then `P @ X_inf` should return `X_inf` unchanged. This confirms the steady-state distribution.

---

## Exercise 3 — Center Data (subtract column means)

Replace the entire `### START CODE HERE ###` block with:

```python
    mean_vector = np.mean(Y, axis=0)
    mean_matrix = np.repeat(mean_vector, Y.shape[0])
    mean_matrix = np.reshape(mean_matrix, Y.shape, order='F')
    
    X = Y - mean_matrix
```

**Intuition:** 
- `np.mean(Y, axis=0)` computes the mean of each column (pixel) across all 55 images.
- `np.repeat` repeats each mean value 55 times (once per observation).
- `np.reshape(..., order='F')` reshapes column-wise so each column gets its own mean repeated.
- Subtracting gives the centered data where each pixel has mean 0.

---

## Exercise 4 — Covariance Matrix

Replace the entire `### START CODE HERE ###` block with:

```python
    n = X.shape[0]
    cov_matrix = np.dot(X.T, X) / (n - 1)
```

**Intuition:** The covariance formula is `Σ = (1/(n-1)) · Xᵀ · X` where X is the centered data. `X.shape[0]` gives `n = 55` (number of observations). The result is a 4096×4096 symmetric matrix.

---

## Exercise 5 — Perform PCA (dimensionality reduction)

Replace the entire `### START CODE HERE ###` block with:

```python
    V = eigenvecs[:, :k]
    Xred = np.dot(X, V)
```

**Intuition:**
- `eigenvecs[:, :k]` selects the first `k` eigenvectors (columns), which correspond to the `k` largest eigenvalues (principal components with most variance).
- `X @ V` projects the centered 55×4096 data onto the k-dimensional subspace, producing a 55×k matrix. Each image is now represented by just k numbers instead of 4096.
