# rl-finlab

Reinforcement Learning Finance Laboratory - A research platform for applying deep reinforcement learning to financial optimization problems.

## Overview

**rl-finlab** explores two fundamental financial optimization challenges using state-of-the-art reinforcement learning algorithms:

1. **Optimal Execution**: Minimize implementation shortfall when liquidating large positions under market impact and transaction costs
2. **Portfolio Allocation**: Maximize risk-adjusted returns on multi-asset portfolios with transaction costs

This project provides clean, modular implementations of RL environments, classical baselines, and training scripts to enable systematic experimentation and ablation studies.

## Key Features

- **Gymnasium-compatible environments** for execution and allocation tasks
- **Classical baselines** for comparison (TWAP, VWAP, Almgren-Chriss, Buy-and-Hold, Momentum, Mean-Reversion)
- **Stable-Baselines3 integration** with PPO and SAC algorithms
- **TOML-based configuration** for reproducible experiments
- **Real market data** via Yahoo Finance with efficient caching
- **Custom neural network architectures** for risk-aware decision making
- **Comprehensive evaluation metrics** (Sharpe ratio, max drawdown, implementation shortfall)

## Project Structure

```
rl-finlab/
├── apps/                      # Training scripts
│   ├── sb3_ppo_exec.py       # Execution task with PPO
│   └── sb3_sac_alloc.py      # Allocation task with SAC
├── src/
│   ├── rl/
│   │   ├── agents/           # Custom policy networks
│   │   ├── baselines/        # Classical algorithms
│   │   ├── envs/             # RL environments
│   │   │   ├── exec_env.py  # Single-asset execution
│   │   │   └── alloc_env.py # Multi-asset allocation
│   │   └── eval/             # Performance metrics
│   └── utils/                # Data utilities
├── experiments/              # Configuration files
├── notebooks/                # Analysis notebooks
├── models/                   # Trained models (gitignored)
└── data/cache/              # Cached market data
```

## Installation

This project uses [Pixi](https://pixi.sh) for environment and dependency management.

1. Install Pixi (if not already installed):
```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

2. Clone and setup the project:
```bash
git clone <repository-url>
cd rl-finlab
pixi install
```

## Quick Start

### Train Execution Model

Train a PPO agent for optimal execution:

```bash
pixi run train_exec
```

This runs the execution task with configuration from [experiments/exec_reward_ablation.toml](experiments/exec_reward_ablation.toml).

### Train Allocation Model

Train a SAC agent for portfolio allocation:

```bash
pixi run train_alloc
```

This runs the allocation task with configuration from [experiments/alloc_risk_reward.toml](experiments/alloc_risk_reward.toml).

### Launch Jupyter Lab

For interactive analysis and visualization:

```bash
pixi run launch-jupyter
```

Navigate to the notebooks in the `notebooks/` directory.

## Environments

### Execution Environment

**Purpose**: Liquidate a single asset position optimally over a fixed time horizon.

**Observations**:
- Inventory remaining (normalized)
- Time fraction remaining

**Actions**: Discrete trading fractions [0%, 5%, 10%, 20%, 40%]

**Dynamics**:
- Geometric Brownian motion for price simulation
- Nonlinear temporary market impact
- Per-share transaction fees

**Reward**: Implementation shortfall (optional inventory penalty for risk control)

### Allocation Environment

**Purpose**: Dynamically allocate capital across multiple assets to maximize risk-adjusted returns.

**Observations**:
- Historical returns (configurable lookback window)
- Current portfolio weights
- Asset volatilities

**Actions**: Continuous portfolio weights on probability simplex

**Dynamics**:
- Real historical price data from Yahoo Finance
- Configurable rebalancing frequency
- Transaction costs (basis points)

**Reward**: Return minus volatility penalty or Sharpe ratio proxy

## Baselines

### Execution Baselines
- **TWAP** (Time-Weighted Average Price): Uniform liquidation schedule
- **VWAP** (Volume-Weighted Average Price): Volume-based liquidation
- **Almgren-Chriss**: Closed-form optimal execution with linear impact

### Allocation Baselines
- **Equal Weight**: Uniform allocation across assets
- **Buy-and-Hold**: Static initial allocation
- **Momentum**: Allocate based on recent performance
- **Mean-Reversion**: Contrarian allocation strategy

## Configuration

Experiments are configured via TOML files in the `experiments/` directory. Example structure:

```toml
[env]
# Environment hyperparameters

[agent]
# Algorithm and network architecture

[reward]
# Reward function specification

[train]
timesteps = 200_000

[eval]
n_episodes = 100
```

## Development

### Code Style

This project uses [Ruff](https://github.com/astral-sh/ruff) for linting and formatting:

- Line length: 79 characters
- Quote style: single quotes

Format code:
```bash
pixi run ruff format .
```

Lint code:
```bash
pixi run ruff check .
```

### Running Custom Scripts

Set the PYTHONPATH to include the src directory:

```bash
PYTHONPATH=.:./src python -m apps.sb3_ppo_exec --config=your_config.toml
```

## Requirements

- Python 3.11
- PyTorch
- Stable-Baselines3
- Gymnasium
- yfinance
- Polars/Pandas
- NumPy

All dependencies are managed via Pixi and specified in [pyproject.toml](pyproject.toml).

## License

Copyright (C) 2025 Harish Naik

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

See [https://www.gnu.org/licenses/](https://www.gnu.org/licenses/) for details.

## Citation

If you use this code in your research, please cite:

```bibtex
@software{rl_finlab,
  title = {rl-finlab: Reinforcement Learning Finance Laboratory},
  author = {Naik, Harish},
  year = {2025},
  version = {0.1.0}
}
```

## Contributing

Contributions are welcome! Please ensure code follows the project's style guidelines and includes appropriate tests.

## Acknowledgments

This project builds on:
- [Stable-Baselines3](https://github.com/DLR-RM/stable-baselines3) for RL algorithms
- [Gymnasium](https://gymnasium.farama.org/) for environment interfaces
- Classical finance literature on optimal execution and portfolio optimization