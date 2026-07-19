<!-- ─────────────────────────── HERO ─────────────────────────── -->
![header](https://capsule-render.vercel.app/api?type=waving&color=0:0F172A,50:4C1D95,100:22D3EE&height=200&section=header&text=Ball%20Balancer%20RL&fontSize=46&fontColor=ffffff&animation=fadeIn&desc=PPO%20%C2%B7%20Gazebo%20%C2%B7%20ROS%20Noetic&descAlignY=58&descSize=18)

<div align="center">

### Keep a ball centered on a tilting table — trained with PPO in Gazebo + ROS.

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![ROS](https://img.shields.io/badge/ROS_Noetic-22314E?style=for-the-badge&logo=ros&logoColor=white)
![Gazebo](https://img.shields.io/badge/Gazebo-F58113?style=for-the-badge&logo=gazebo&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)

<a href="#-quick-start">
  <img src="https://img.shields.io/badge/📖_Docs-Train_%26_Test-8b5cf6?style=for-the-badge" alt="Docs"/>
</a>
&nbsp;
<a href="#-features">
  <img src="https://img.shields.io/badge/🤖_Stack-PPO_%2B_Gymnasium-22c55e?style=for-the-badge" alt="Stack"/>
</a>

</div>

---

## Overview

This project balances a rolling ball on a two-axis tilting table inside **Gazebo**, controlled by a **Proximal Policy Optimization (PPO)** agent via **ROS Noetic** and **Stable-Baselines3**.

The agent observes ball state, commands continuous joint efforts on `joint_x` / `joint_y`, and learns to keep the ball near the plate center for as long as possible.

**Course:** COMP3766 *Introduction to Robotic Manipulation* — Memorial University of Newfoundland  
**Instructor:** Dr. Vinicius Prado da Fonseca

> [!NOTE]
> A trained checkpoint ships in-repo (`ppo_tilting_table_final.zip` + `trained_models/`) so you can evaluate without a full retrain.

---

## Features

| | Feature | Description |
|--|---------|-------------|
| | **Gazebo physics world** | Tilting table + rolling ball with realistic dynamics. |
| | **Custom joint plugin** | `universal_joint_controller.cpp` applies effort commands from `/tilting_table/tilt_cmd`. |
| | **Gymnasium env** | 4D observation (relative x, y, vx, vy); continuous 2D action (joint efforts). Optional 8D obs variant. |
| | **PPO training** | `train_ppo.py` with checkpoints, eval callbacks, TensorBoard logs. |
| | **Eval harness** | `test_ppo.py` loads a saved model and rolls out episodes. |
| | **Ball tracker util** | `ball_tracker.py` logs plate-relative ball pose from `/gazebo/model_states`. |

> [!TIP]
> Reward centers the ball and lightly penalizes large actions — tune those constants first if the policy is sluggish or twitchy.

---

## Built with

<p align="center">
  <img src="https://skillicons.dev/icons?i=python,cpp,ros,pytorch&perline=4" alt="Tech stack"/>
</p>

| Layer | Tools |
|-------|--------|
| Simulation | Gazebo 11, ROS Noetic, catkin |
| Control | Custom C++ Gazebo plugin, effort commands |
| RL | Gymnasium, Stable-Baselines3 (PPO), PyTorch |
| Logging | TensorBoard |

---

## Quick start

> [!IMPORTANT]
> Target environment: **Ubuntu 20.04**, **ROS Noetic Desktop-Full**, Gazebo 11, Python 3.8+.

### 1. Workspace + clone

```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
git clone https://github.com/bhaskaraanjana/Simulated-Ball-Balancing-RL.git table_env_pkg
cd ~/catkin_ws
```

### 2. Python deps

```bash
# optional venv
python3 -m venv .venv && source .venv/bin/activate
pip install -r src/table_env_pkg/requirements.txt
```

Key packages: `stable-baselines3`, `gymnasium`, `numpy` (see `requirements.txt`). ROS packages install via `apt`, not pip.

### 3. Build & source

```bash
cd ~/catkin_ws
catkin_make   # or: catkin build
source devel/setup.bash
```

### 4. Simulate

```bash
roslaunch table_env_pkg start_sim.launch
```

### 5. Train

```bash
cd ~/catkin_ws/src/table_env_pkg/scripts/
python3 train_ppo.py
# tensorboard --logdir ~/catkin_ws/training_logs/
```

### 6. Test a trained agent

Place models under `~/catkin_ws/trained_models/` (or use the shipped zip), then:

```bash
python3 test_ppo.py
```

---

## Environment design

| | Spec |
|--|------|
| **Observation (4D)** | Ball position & velocity relative to plate |
| **Action** | Continuous effort on `joint_x`, `joint_y` |
| **Reward** | Near-center bonus − action magnitude penalty |
| **Done** | Ball falls off (Z / XY bounds) or max steps |

---

## Roadmap

- Train/evaluate the **8D** observation variant (joint angles + velocities)
- Hyperparameter search (Optuna / Ray Tune)
- Richer reward (velocity damping near center)
- Faster resets via `ros_control` position controllers
- Disturbance / robustness test suite

---

## License

Course project materials — use and extend with attribution to MUN COMP3766 and the original authors.

![footer](https://capsule-render.vercel.app/api?type=waving&color=0:0F172A,50:4C1D95,100:22D3EE&height=100&section=footer)
