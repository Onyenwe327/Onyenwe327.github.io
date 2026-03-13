---
layout: page
title: Game AI — Street Fighter II
description: PPO and Recurrent PPO agents for complex fighting game control
img: assets/img/maxresdefault.jpg
importance: 1
category: research
related_publications: false
---

This project investigates how reinforcement learning can master a complex,
partially observable fighting game environment — **Street Fighter II** —
using Proximal Policy Optimization (PPO) and Recurrent PPO (LSTM-based).

Unlike simple arcade environments, Street Fighter presents:

- High-dimensional pixel observations  
- Long-horizon credit assignment  
- Sparse win/loss rewards  
- Delayed effects of combos and defensive strategies  

The objective was to design a stable training pipeline capable of achieving competitive performance against built-in AI opponents.

---

# Environment & Engineering Design

The environment was built using **OpenAI Gym Retro**.

Several key modifications were introduced to improve training stability:

### 1. Custom Reset Logic
Health values and match states were preserved across rounds to avoid information loss during environment resets.

### 2. Vectorized Environments
Replaced `DummyVecEnv` with `SubprocVecEnv` to enable multiprocessing-based parallel rollout collection, significantly improving throughput.

### 3. Modularized Game Logic
Refactored long conditional blocks into modular reward and event-handling functions for better maintainability and extensibility.

---

# Reward Function Design

A naive reward based only on score signals was insufficient.

A domain-informed reward function was designed incorporating:

- Damage dealt to opponent  
- Damage received  
- Match win/loss bonus  
- Combo execution reward  
- Special move incentives  
- Penalties for inactivity or failed execution  

This significantly improved learning efficiency and policy quality.

The reward shaping process was iterative and adjusted based on training dynamics.

---

# PPO vs Recurrent PPO

Two algorithms were evaluated:

## PPO

- Feed-forward policy network  
- Stable clipping objective  
- Strong baseline performance  

## Recurrent PPO (LSTM-based)

Street Fighter contains partial observability and temporal dependencies.  

To address this, LSTM layers were introduced to capture:

- Sequential attack patterns  
- Combo timing dependencies  
- Opponent behavioral cycles  

Recurrent PPO demonstrated improved stability in higher difficulty settings.

---

# Demo

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item"
          src="https://www.youtube.com/embed/6MxL7OmCIMM"
          allowfullscreen></iframe>
</div>

---

# Performance Results

The final PPO agent achieved:

- **96% win rate** against built-in AI
- Stable convergence across multiple runs
- Competitive performance at elevated game difficulty

Recurrent PPO showed stronger consistency in longer matches and higher-level AI settings.

---

# Training Efficiency Improvements

To accelerate training:

- Grayscale encoding reduced input dimensionality
- Frame skipping reduced redundant transitions
- Hyperparameter search was simplified to reduce iteration latency
- Vectorized environment parallelism improved sample throughput

These optimizations yielded approximately **30% improvement in training efficiency**.

---

# Technical Stack

- Python
- PyTorch
- Stable-Baselines3
- Gym Retro
- SubprocVecEnv
- LSTM-based policy networks

---

# Recognition

Awarded **Best Final Year Project**  
(top-ranked project in the department)  
University of Macau, 2023.

---

# Broader Impact

This project demonstrates that:

- Careful reward design is critical in complex environments
- Memory mechanisms (LSTM) matter in partially observable games
- Engineering decisions (parallel environments, reset logic) directly affect RL performance

It laid the foundation for my subsequent research in reinforcement learning for robotics and control.