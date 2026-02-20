# Week 4 Graded Quiz — Answers & Solutions

**Course:** Linear Algebra for Machine Learning and Data Science (DeepLearning.AI)  
**Score Target:** 78% to pass | **Previous Score:** 70% (7/10)

---

## Q1. Rank of Linear Transformation T

**Question:** Given:
$$T\left(\begin{bmatrix}1\\0\\0\end{bmatrix}\right)=\begin{bmatrix}1\\3\\-4\end{bmatrix},\quad T\left(\begin{bmatrix}0\\1\\0\end{bmatrix}\right)=\begin{bmatrix}2\\-1\\-3\end{bmatrix},\quad T\left(\begin{bmatrix}0\\0\\1\end{bmatrix}\right)=\begin{bmatrix}4\\5\\-11\end{bmatrix}$$
Find the rank.

**Answer: 2**

The transformation matrix (columns = outputs on standard basis):
$$A = \begin{bmatrix}1&2&4\\3&-1&5\\-4&-3&-11\end{bmatrix}$$

Check if column 3 is a combo of columns 1 & 2:  
$2\begin{bmatrix}1\\3\\-4\end{bmatrix}+1\begin{bmatrix}2\\-1\\-3\end{bmatrix}=\begin{bmatrix}4\\5\\-11\end{bmatrix}$ ✅

Third column is dependent → **Rank = 2**.

---

## Q2. Determinant Properties (Select all true)

**Question:** Let $M$ be a square matrix. Check all that are true.

**Answers:**

- ✅ If $\det(M)=5$, then $\det(M^n)=5^n$
  - Product rule: $\det(M^n)=\det(M)\cdot\det(M)\cdots=5^n$
- ✅ If $M$ is non-singular, then $M^{-1}$ is also non-singular
  - $\det(M^{-1})=\frac{1}{\det(M)}\neq 0$
- ❌ An $n\times n$ matrix has $n$ distinct eigenvalues — *Can have repeated eigenvalues*
- ❌ Determinant is always positive — *Can be negative*

---

## Q3. det(M·N)

**Question:** Given:
$$M=\begin{bmatrix}1&0&1\\0&1&0\\1&1&1\end{bmatrix},\quad N=\begin{bmatrix}2&8&7\\4&3&9\\1&9&5\end{bmatrix}$$
Find $\det(M\cdot N)$.

**Answer: 0**

Row 3 of $M$ = Row 1 + Row 2 → rows are dependent → $\det(M)=0$.  
$\det(M\cdot N)=\det(M)\cdot\det(N)=0\cdot\det(N)=0$.

---

## Q4. Span of Vectors

**Question:** What is the span of $\begin{bmatrix}2\\1\\1\end{bmatrix},\ \begin{bmatrix}1\\0\\2\end{bmatrix},\ \begin{bmatrix}1\\0\\1\end{bmatrix}$?

**Answer: The entire 3-dimensional space**

$$\det\begin{bmatrix}2&1&1\\1&0&0\\1&2&1\end{bmatrix}=1\neq 0$$

Three linearly independent vectors in ℝ³ span all of ℝ³ and form a basis.

---

## Q5. Select All Bases for 3D Space

**Question:** Which of the following are bases for ℝ³?

**Answers:**

| Set | det | Basis? |
|-----|-----|--------|
| $\begin{bmatrix}2\\3\\1\end{bmatrix},\begin{bmatrix}0\\-1\\2\end{bmatrix},\begin{bmatrix}-1\\1\\4\end{bmatrix}$ | $-19$ | ✅ Yes |
| $\begin{bmatrix}1\\0\\1\end{bmatrix},\begin{bmatrix}0\\1\\0\end{bmatrix},\begin{bmatrix}1\\0\\0\end{bmatrix}$ | $-1$ | ✅ Yes |
| $\begin{bmatrix}-1\\2\\1\end{bmatrix},\begin{bmatrix}1\\2\\2\end{bmatrix},\begin{bmatrix}1\\0\\1\end{bmatrix}$ | $-3$ | ✅ Yes |
| $\begin{bmatrix}2\\3\\1\end{bmatrix},\begin{bmatrix}0\\2.5\\3.5\end{bmatrix},\begin{bmatrix}0\\5.5\\4.5\end{bmatrix}$ | $-16$ | ✅ Yes |
| $\begin{bmatrix}-1\\2\\1\end{bmatrix},\begin{bmatrix}1\\2\\2\end{bmatrix},\begin{bmatrix}1\\6\\5\end{bmatrix}$ | $0$ | ❌ No |

**Select the first four options. The last set is linearly dependent (det = 0).**

---

## Q6. Characteristic Polynomial

**Question:** Find the characteristic polynomial for $M=\begin{bmatrix}2&1\\-3&6\end{bmatrix}$.

**Answer:** $\lambda^2-8\lambda+15$

$$\det(M-\lambda I)=(2-\lambda)(6-\lambda)-(-3)(1)=\lambda^2-8\lambda+12+3=\lambda^2-8\lambda+15$$

---

## Q7. Covariance Matrix

**Question:** Find the covariance matrix for $M=\begin{bmatrix}3&2\\5&8\end{bmatrix}$.

**Answer:** $\begin{bmatrix}2&6\\6&18\end{bmatrix}$

- Means: $\mu_x=4,\ \mu_y=5$
- Centered: $X=\begin{bmatrix}-1&-3\\1&3\end{bmatrix}$
- $\Sigma=\frac{1}{n-1}X^TX=\begin{bmatrix}-1&1\\-3&3\end{bmatrix}\begin{bmatrix}-1&-3\\1&3\end{bmatrix}=\begin{bmatrix}2&6\\6&18\end{bmatrix}$

---

## Q8. Eigenvectors

**Question:** Find all eigenvectors of $M=\begin{bmatrix}3&0\\-2&1\end{bmatrix}$.

**Answers:**

Eigenvalues from $(3-\lambda)(1-\lambda)=0$: $\lambda=3$ and $\lambda=1$.

- ✅ $\begin{bmatrix}0\\k\end{bmatrix}$ for any $k$ — eigenvector for $\lambda=1$ (since $3x=x\Rightarrow x=0$)
- ✅ $\begin{bmatrix}k\\-k\end{bmatrix}$ for any $k$ — eigenvector for $\lambda=3$ (since $-2x+y=3y\Rightarrow y=-x$)
- ❌ $\begin{bmatrix}1\\3\end{bmatrix}$ — does not satisfy either eigenvalue equation
- ❌ $\begin{bmatrix}k\\k\end{bmatrix}$ — does not satisfy either eigenvalue equation

---

## Q9. X − μ Matrix (PCA)

**Question:** Given houses dataset, find the $X-\mu$ matrix.

| | Size (m²) | Bedrooms | Bathrooms |
|---|---|---|---|
| House 1 | 70 | 2 | 2 |
| House 2 | 110 | 4 | 2 |

**Answer:** $X-\mu=\begin{bmatrix}-20&-1&0\\20&1&0\end{bmatrix}$

- $\mu_{\text{size}}=90,\ \mu_{\text{bed}}=3,\ \mu_{\text{bath}}=2$
- Rows = data points, columns = features → result is a **2×3 matrix**

> ⚠️ Don't confuse with $(X-\mu)^T$ which is 3×2!

---

## Q10. Eigenvalues of Covariance Matrix

**Question:** From Q9, find the eigenvalues of the covariance matrix.

**Answer:** $\lambda_1=802,\ \lambda_2=0,\ \lambda_3=0$

Covariance matrix:
$$C=\frac{1}{n-1}(X-\mu)^T(X-\mu)=\begin{bmatrix}800&40&0\\40&2&0\\0&0&0\end{bmatrix}$$

- Third feature (bathrooms) has zero variance → $\lambda_3=0$
- Upper-left 2×2: $(800-\lambda)(2-\lambda)-1600=0$ → $\lambda^2-802\lambda=0$ → $\lambda=0$ or $\lambda=802$

---

## 🎯 Fix These 3 to Get 100%

| Q | Wrong Answer | Correct Answer |
|---|---|---|
| **Q1** | 3 | **2** |
| **Q5** | Missed options | **Select first 4** |
| **Q9** | Chose 3×2 transpose | **2×3 matrix: [[-20,-1,0],[20,1,0]]** |
