# Inferring Reward-Punishment Sensitivity from Users' Behavior via Online Learning in Digital Behavior Change Systems

Replication materials for the paper submitted to **IEEE/WIC WI-IAT 2026**.

---

## Overview

This repository contains the simulation code and output data accompanying the paper. The study proposes a learning-based framework that infers users' latent sensitivity to rewards and punishments directly from behavioral observations — without self-reports, prior user data, or hand-crafted psychological features.

Two online learning algorithms are implemented and compared:

- **Q-Learning** (Reinforcement Learning) with outcome-guided ε-greedy exploration
- **Thompson Sampling** (Multi-Armed Bandit) with Beta-Bernoulli arms
- **Random baseline** — non-adaptive reference condition

The simulation environment models a physical activity behavior change scenario (daily step counts) over 100 days, with a population of 30 users divided into reward-sensitive (Group 1) and punishment-sensitive (Group 2) profiles.

---

## Repository Structure

```
.
├── ModelsLearning_RL_MAB_Rand_final.ipynb   # Main simulation notebook (self-contained)
├── daily_detail_seed42.xlsx                 # Day-by-day detail for the representative run (seed=42)
├── detail_results.xlsx                      # Full per-user, per-day results across all 20 runs
├── per_run_metrics.xlsx                     # Aggregate metrics per run (seeds 42–61)
├── table1_qtable_g01.xlsx                   # Table I: example Q-table, reward-sensitive user
├── table2_bandit_g02.xlsx                   # Table II: bandit parameters log(α/β), punishment-sensitive user
├── table3_mean_values.xlsx                  # Table III: mean learned values aggregated by group (RL and MAB)
├── table4_aggregate_metrics.xlsx            # Table IV: convergence metrics across 20 runs
├── table4_convergence.xlsx                  # Table IV (alternative format)
├── figure1_single_user_dynamics.png         # Figure 1: single-user learning dynamics (MAB and RL)
└── figure2_avg_step_trends.png              # Figure 2: average daily step trends across approaches
```

---

## Reproducing the Results

### Requirements

```
python >= 3.9
numpy
pandas
matplotlib
scikit-learn
skforecast
scipy
```

Install dependencies:

```bash
pip install numpy pandas matplotlib scikit-learn skforecast scipy
```

### Running the notebook

Open `ModelsLearning_RL_MAB_Rand_final.ipynb` in Jupyter and run all cells in order. The notebook is self-contained: it generates the synthetic user population, runs all three conditions (Q-Learning, Thompson Sampling, Random baseline) over 20 independent seeds (42–61), and produces all tables and figures reported in the paper.

Outputs are saved to a `results/` folder created automatically at runtime.

---

## Simulation Design

| Parameter | Value |
|---|---|
| Users per group | 15 (Group 1: reward-sensitive, Group 2: punishment-sensitive) |
| Simulation days | 100 per run |
| Independent runs | 20 (seeds 42–61) |
| Step history | ~6 months per user |
| Action space | 9 configurations: (r, p) with r + p = 1, r ∈ {0.1, 0.2, …, 0.9} |
| RL state space | {0 = failure, 1 = success} (previous day's outcome) |
| RL parameters | α = 0.1, γ = 0.9, ε = 0.2 |
| MAB prior | Beta(α=1, β=1) per arm, per state |
| Goal prediction | Linear Regression with autoregressive features (3 lags), retrained daily via backtesting |

Step history distributional parameters are grounded in the [PMData dataset](https://dl.acm.org/doi/10.1145/3339825.3394926), which provides open-access longitudinal physical activity data from real individuals.

---

## Key Results

### RQ1 — Sensitivity Inference (Table III, Table IV)

Both algorithms successfully infer users' latent reward-punishment sensitivity from behavioral outcomes alone, with no self-report or prior knowledge:

| Model | Convergence t\* |
|---|---|
| Q-Learning | 9.2 ± 0.7 days |
| Thompson Sampling | 17.7 ± 1.0 days |

*t\* = first day from which the selected action remains in the correct sensitivity region for 7 consecutive days, averaged over 20 runs.*

The internal representations (Q-values for RL, log(α/β) for MAB) show a clear and interpretable group-level structure: reward-dominant configurations accumulate high scores for Group 1, punishment-dominant configurations for Group 2, with the reverse pattern in both cases.

### RQ2 — Behavioral Outcomes (Figure 2)

Both adaptive learners consistently outperform the non-adaptive random baseline in terms of sustained increases in daily step counts across both user groups. The relative advantage of Q-Learning vs. Thompson Sampling differs between groups, reflecting distinct alignment between learning mechanism and sensitivity profile:

- **Q-Learning** shows stronger gains for punishment-sensitive users (Group 2)
- **Thompson Sampling** performs more effectively for reward-sensitive users (Group 1)

---

## Theoretical Background

The framework is grounded in:

- **Reinforcement Sensitivity Theory** (Gray, 1970) — Behavioral Activation System (BAS, reward sensitivity) and Behavioral Inhibition System (BIS, punishment sensitivity)
- **Goal Setting Theory** (Locke & Latham, 2002) — personalized, attainable daily step goals derived from individual behavioral history

Users' latent sensitivity is never observed directly; it is inferred from the structure of learned Q-values (RL) and Beta posterior distributions (MAB) over the reward-punishment action space.

---

## License

The code and data in this repository are released for research and reproducibility purposes.

---

## Citation

If you use this code or data, please cite the paper once it is published. The BibTeX entry will be added upon acceptance.
