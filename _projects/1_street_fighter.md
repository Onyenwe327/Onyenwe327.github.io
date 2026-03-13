---
layout: page
title: Reinforcement Learning in Games
description: This project builds a competitive deep RL agent for Street Fighter II using PPO and Recurrent PPO.
img: assets/img/maxresdefault.jpg
importance: 1
category: research
---

**Role:** RL Developer  
**Foundation:** Extended and refactored an existing Gym Retro implementation  
**Final Result:** 96% win rate vs built-in AI  

---

# System Engineering Modifications

The original project was extended with significant architectural changes.

### 1. Custom Environment Reset Logic

Modified reset behavior to preserve remaining health across rounds, preventing information loss between episodes.

This improved reward consistency and reduced instability during match transitions.

### 2. Reward System Redesign

The original implementation relied primarily on score signals.

I redesigned the reward to incorporate:

- Damage dealt / received
- Match wins and losses
- Combo execution
- Special move incentives
- Inactivity penalties
- Combo counter constraints to avoid exploitation

Reward shaping was iteratively tuned based on convergence diagnostics.

### 3. Code Refactoring

- Split long if-elif logic into modular reward/event handlers
- Improved readability and maintainability
- Added guard logic for corner cases

This allowed faster iteration during experimentation.

---

# Parallelization & Training Efficiency

### Vectorized Environment Upgrade

Replaced `DummyVecEnv` with `SubprocVecEnv` to enable multiprocessing-based rollout collection.

This significantly improved training throughput for computationally heavy pixel-based environments.

### Hyperparameter Search Decision

Initial hyperparameter tuning required ~19 hours for 100 trials.

To accelerate development cycles, I removed exhaustive search and focused on stable default configurations.

This reduced iteration latency and improved experimental efficiency.

### Observation Optimization

- Grayscale preprocessing
- Frame skipping
- Reduced input size to (84,84,1)

Overall training speed improved by ~30%.

---

# PPO vs Recurrent PPO

Recurrent PPO (LSTM):

- Modeled temporal dependencies
- Captured combo timing and opponent behavior cycles
- Improved consistency under higher difficulty settings

---
<iframe width="100%" height="450"
        src="https://www.youtube.com/embed/6MxL7OmCIMM"
        frameborder="0"
        allowfullscreen>
</iframe>
# Performance

- 96% win rate over 100 evaluation matches
- Stable convergence across runs
- Competitive performance at elevated game difficulty