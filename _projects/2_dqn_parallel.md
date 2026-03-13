---
layout: page
title: Parallelization of Q-Learning & DQN
description: Synchronization strategies and asynchronous environment parallelization for reinforcement learning
img: assets/img/7.jpg
importance: 2
category: research
---

Reinforcement learning is computationally expensive due to sequential environment interaction and value updates.  
This project investigates parallelization strategies for both **tabular Q-learning** and **Deep Q-Network (DQN)**, analyzing trade-offs between synchronization cost, convergence stability, and throughput.

---

## Problem Formulation

Parallel RL introduces two fundamental challenges:

- **Race conditions**: concurrent updates to shared Q-values or neural network weights.
- **Synchronization overhead**: locking and communication reduce effective speedup.

The goal is to design a synchronization strategy that preserves convergence while maximizing computational efficiency.

---

# Part I — Parallel Q-Learning

## Experimental Setup

- Environment: FrozenLake  
- Platform: MacBook Pro (8 cores, 16GB RAM)  
- Threads: 1, 4, 8  
- Episodes: 1000  
- Grid size: 1000 × 1000  

---

## Parallelization Strategies

### 1. Lock-Free Updates

Multiple threads update the global Q-table without synchronization.

- Maximum throughput
- Risk of inconsistent updates
- Potential instability

---

### 2. Lock-Based Updates

Each update is protected by a mutex.

- Guaranteed correctness
- High lock contention
- Often serializes computation

---

### 3. Local Copies with Periodic Synchronization

Each thread maintains a local Q-table.

- Local updates reduce contention
- Periodic merging (averaging)
- Maintains convergence while improving speed

---

## Q-Learning Results

| Threads | Serial | Lock-Based | Periodic Sync |
|----------|--------|------------|---------------|
| 4 | 197 s | 543 s | 125 s |
| 8 | — | 987 s | 83 s |

### Observations

- Lock-based strategy removes nearly all parallel benefit.
- Periodic synchronization achieves:
  - ~2× speedup (4 threads)
  - ~3× speedup (8 threads)
- Convergence behavior remains stable under periodic merging.

This validates that synchronization granularity is the dominant factor in parallel RL performance.

---

# Part II — Deep Q-Network (DQN)

Tabular Q-learning does not scale to high-dimensional state spaces.

DQN replaces the Q-table with a neural network and introduces:

- Experience replay
- Target network stabilization
- Function approximation

---

## Asynchronous Environment Parallelization

Instead of parallelizing weight updates directly, we parallelize **data collection**.

Multiple asynchronous environments:

- Run independently
- Generate diverse trajectories
- Feed a shared replay buffer

The Q-network is trained from aggregated samples.

This decouples environment stepping from network optimization and reduces synchronization cost.

---

## Experimental Setup (DQN)

- Environment: Atari Pong (Arcade Learning Environment)
- Platform: CARC HPC
- Asynchronous environments: 1, 4, 8
- Total timesteps: 1M
- 10 runs averaged
- CPU and GPU evaluated

---

## Training Time Comparison

### CPU

| Setup | Training Time |
|--------|---------------|
| 1 env | 29,532 s |
| 4 envs | 11,423 s |
| 8 envs | 6,311 s |

- 4 envs → 60% reduction
- 8 envs → 78% reduction

---

### GPU

| Setup | Training Time |
|--------|---------------|
| 1 env | 8,674 s |
| 4 envs | 5,987 s |
| 8 envs | 3,574 s |

- GPU gives 3.40× speedup over CPU (serial case)
- Asynchronous envs still provide:
  - 30% reduction (4 envs)
  - 58% reduction (8 envs)

---

## Example Training Curve

{% include figure.liquid path="assets/img/9.jpg" title="DQN Training Curve (Pong)" class="img-fluid rounded z-depth-1" %}

The average reward increases steadily after ~0.4M frames, indicating stable convergence under asynchronous sampling.

---

# Analysis

## GPU vs CPU

GPU acceleration significantly reduces network update cost.  
However, as environment parallelization increases, environment stepping becomes less dominant relative to model training.

---

## Asynchronous Environments

Ideal theoretical speedup:

Speedup ≈ N environments

Practical speedup is lower due to:

- Synchronization overhead
- Replay buffer contention
- Target network updates
- Environment stepping cost

Still, substantial empirical improvements are observed.

---

# Key Takeaways

1. Fine-grained locking eliminates parallel benefits.
2. Periodic synchronization enables stable and efficient tabular parallelization.
3. For DQN, parallelizing **data collection** is more effective than parallelizing weight updates.
4. Combining GPU acceleration with asynchronous environments yields the strongest performance gains.

---

# Technical Stack

- C++, Python
- OpenMP
- PyTorch
- CUDA
- Gymnasium
- Arcade Learning Environment

---

# Broader Implications

Parallelization is essential for scaling reinforcement learning to real-world tasks.  
Designing synchronization-aware training pipelines enables:

- Faster convergence
- Better hardware utilization
- Improved scalability across CPU and GPU systems