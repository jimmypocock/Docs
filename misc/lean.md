# LEAN

## Description

LEAN is an open-source algorithmic trading engine that enables strategy development, backtesting, and live trading across multiple asset classes. It provides a professional-grade infrastructure for quantitative trading with support for Python and C# programming languages.

## URL/Installation

- Website: <https://www.lean.io/>
- GitHub: <https://github.com/QuantConnect/Lean>
- Installation: `pip install lean`

## Tags

# tool #algo-trading #intermediate #cli #framework #open-source #cross-platform #multi-asset

## Use Cases

- Developing and backtesting quantitative trading strategies
- Live trading with multiple brokerages and asset classes
- Algorithmic trading research with alternative data sources
- Local development with cloud deployment capabilities
- Multi-asset portfolio management and optimization

## Key Features

- Support for Python and C# programming languages
- Event-driven architecture designed for algorithm development
- Multi-asset support (Equities, Forex, Options, Futures, Crypto, CFDs, and more)
- Strategy Development Framework with plug-and-play modules
- Cross-platform compatibility (Windows, MacOS, Linux)
- Integration with 40+ data sources and multiple brokerages
- Local development with cloud sync capabilities
- Research environment with Jupyter notebooks

## Installation/Setup

For basic installation with pip:

```bash
# Install LEAN CLI
pip install lean

# Initialize a workspace
lean init

# Log in to QuantConnect (optional for cloud features)
lean login
```

For a more comprehensive setup:

```bash
# Clone the repository
git clone https://github.com/QuantConnect/Lean.git
cd Lean

# Install Docker (required for local execution)
# Follow Docker installation instructions for your OS

# Run a backtest with the CLI
lean backtest "MyAlgorithm"
```

## Basic Usage

Create and run a simple algorithm:

```bash
# Create a new algorithm project
lean create-project "MyFirstAlgorithm" --language python

# Run a backtest
lean backtest "MyFirstAlgorithm"

# Push to the cloud
lean cloud push "MyFirstAlgorithm"

# Run a cloud backtest
lean cloud backtest "MyFirstAlgorithm"
```

## Configuration

The configuration file for LEAN CLI is created during initialization. You can modify settings with the config commands:

```bash
# View configuration
lean config list

# Set a configuration value
lean config set key value

# Get a configuration value
lean config get key
```

Example configuration for Interactive Brokers:

```json
{
  "ib-tws-dir": "/path/to/IB/tws",
  "ib-controller-dir": "/path/to/IB/controller",
  "ib-user-name": "your-username",
  "ib-account": "your-account-id",
  "ib-use-tws": false,
  "ib-port": 4002
}
```

## Alternatives

- Backtrader: Python-based backtesting framework with a simpler learning curve but fewer features for professional trading
- Zipline: Python backtesting framework originally developed by Quantopian, now community-maintained but with less active development
- PyAlgoTrade: Simpler Python framework for algorithmic trading but with fewer features and less community support
- QuantRocket: Commercial platform based on Zipline with additional features and data sources

## Pros/Cons

**Pros:**

- Professional-grade architecture supporting multiple asset classes simultaneously
- Strong community support and active development
- Open-source with commercial applications permitted
- Seamless transition from backtesting to live trading
- Cross-platform compatibility with Docker support
- Integrates with numerous data providers and brokerages

**Cons:**

- Steeper learning curve compared to simpler frameworks
- Core architecture is in C# which can be challenging for Python-only developers
- Running locally requires Docker and appropriate hardware resources
- Advanced features may require QuantConnect subscription for cloud usage
- Data acquisition for local deployment can be complex

## Date Added

2025-05-19

## Last Updated

2025-05-19

## Notes

LEAN is the engine powering QuantConnect's web-based algorithmic trading platform. It has been developed by a global community of over 180 engineers and powers more than 300 hedge funds. The LEAN CLI simplifies local development while providing access to cloud resources when needed. For optimal performance, ensure Docker is properly configured on your system and consider using Visual Studio Code for development.
