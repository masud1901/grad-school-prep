# Grad School Prep

Personal study materials and course prep: Computer Networks, Math for ML/DS, and Convex Optimization.

## Contents

| Folder | Description |
|--------|-------------|
| **mehdi papers** | Mehdi Beitollahi's FL papers (Lu-lab + later): IoTJ survey, Layer Norm, FedPFT, NoT, LoRA, etc. See `notes/Lu_and_Beitollahi_Reading_List.md`. |
| **steiger papers** | Juaren Steiger's bandit/scheduling papers with Ning Lu: POSS, Backlogged Bandits, Delayed Semi-Bandit (INFOCOM). See folder `README.md`. |
| **Computer Networks** | CS-style fundamentals: Tanenbaum textbook, lectures, and notes (protocols, TCP/IP, wireless). See `Computer Networks/Fundamentals/CLAUDE.md` for the study guide. |
| **math for ML and DS** | [DeepLearning.AI Mathematics for Machine Learning and Data Science](https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science) specialization: assignments (Jupyter), solutions, and graded quiz answers. **Completed.** Covers: Linear transformations (C1), Calculus (C2), Stat and Probability (C3). |
| **Convex Optimization** | [Stanford Online: Convex Optimization](https://learning.edx.org/course/course-v1:StanfordOnline+SOE-YCVX0001+1T2020/home) (edX, SOE-YCVX0001): course materials and Boyd book PDF. |

## ✅ Completed

- **Math for ML & DS specialization** — Linear transformations, Calculus, Probability & Statistics
- **FL papers** — Mehdi's IoTJ FL survey read; **Layer Normalization in Label-Skewed FL** (Zhang et al., TMLR 2024) — full read with detailed notes in `notes/02_Layer_Normalization_Label_Shift_FL.md`; **DSFL** (Beitollahi, Liu, Lu, ICCSPA 2022) — read (two-level sparsification, LSS, top-K, error accumulation; basis for ADAFL proposal); **FLAC** (Beitollahi, Lu, GLOBECOM 2022) — read, notes in `notes/FLAC_Paper_Notes_Masud.md`

## 🎯 March 2026 Goals (updated)

### ~~Math for ML & DS — Probability & Statistics~~ ✓ Done
- [x] Complete the remaining ~50% of the course
- [x] Solid grasp of: Bayes' rule, conditional distributions, expectations
- [x] Finish all assignments and graded quizzes

### Convex Optimization (slow burn — ~2–3 hrs/week)
- [ ] Work through Chapter 1: Convex sets
- [ ] Work through Chapter 2: Convex functions
- [ ] Goal is intuition, not speed — don't force it

### FL Papers (research reading)

**Principle: Finish the Mehdi + Ning Lu FL core before moving to Steiger (bandits), RL specialization, or networks.** Your proposal is FL-first (scheduling + FLAC); knowing FLAC/DSFL and non-IID theory makes the rest click.

- [x] Skim Mahdi's IoTJ FL survey (`mehdi papers/01_...`)
- [x] **Layer Normalization in Label-Skewed FL** (Zhang et al.) — full read; notes in `notes/02_Layer_Normalization_Label_Shift_FL.md` (theory, experiments, takeaways, writing/storytelling)
- [x] **FLAC** (Beitollahi, Lu) — read; convergence and algorithm notes in `notes/FLAC_Paper_Notes_Masud.md`. *Short conference paper (GLOBECOM).*
- [x] **DSFL** (Beitollahi, Liu, Lu, ICCSPA 2022) — read. Two-level sparsification (LSS + top-K), error accumulation; static K from truncated normal → ADAFL extends with loss-feedback adaptive K.
- [ ] FedAvg / FedProx / SCAFFOLD (see `notes/FL_Must_Read_Mehdi_Level.md`)
- [ ] Identify which math tools show up most across FL literature

*Then:* Steiger papers (POSS, Backlogged Bandits), RL Course 1+, networks.

### Markov Chains Crash Course (1 week)
- [ ] Watch MIT OCW / YouTube lectures on discrete Markov chains
- [ ] Cover: states, transition matrices, stationary distributions
- [ ] Enough to be comfortable with MDP notation for RL Course 1

### RL Specialization — Course 1 (recommended next)
- [ ] Start Course 1 (Fundamentals of RL) by end of March
- [ ] Complete at least Week 1–2: k-armed bandits, MDPs

---

## Structure

- **PDFs**: Lectures, textbook chapters, and handouts are kept in the repo.
- **Markdown**: `CLAUDE.md` is the main study guide for the networks material; other `.md` files are solutions and quiz answers.
- **Notebooks**: Math for ML & DS — `Linear transfromations/` (C1W2–C1W4), `Calculus/` (C2_W1–C2_W3), `Stat and Probability/` (C3W1, C3_W2, C3W4).

## What to do next

1. **FL papers (Mehdi + Ning Lu) first** — Complete FLAC, DSFL, and the non-IID core (FedAvg, FedProx, SCAFFOLD) from `notes/FL_Must_Read_Mehdi_Level.md` before diving into Steiger (bandits) or RL. Your proposal is FL-first; this order pays off.
2. **RL Specialization, Course 1** — After the FL core: your math base is ready for [Fundamentals of Reinforcement Learning](https://www.coursera.org/learn/fundamental-reinforcement-learning). Do the short **Markov chains** crash course first if MDPs feel new.
3. **Convex Optimization** — Continue the slow burn (Ch 1–2) when you want theory that ties into FL and control.

---

*Private study repo — course materials and personal notes.*


May 21, 2026    →  RTSS 2026 (A*, stretch — needs RT framing)
Jun 5, 2026     →  CoNEXT 2026 (A, networking backup)
Jul 24, 2026    →  INFOCOM 2027 (A*, PRIMARY TARGET)
Aug 1, 2026     →  AAAI 2027 (A*, strong AI backup, Montreal!)
Rolling         →  IEEE TNNLS / ToN journal track