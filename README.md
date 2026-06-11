# OrderFlowOnFutures

Research project for order flow analysis on futures markets using **VectorBT Pro**.

## Overview

This project explores order flow dynamics in futures markets, leveraging high-frequency data, advanced indicators, and portfolio simulation techniques. Built on Python with GPU acceleration support (MPS on Apple Silicon).

## Features

- **Order Flow Analysis**: Imbalance detection, footprint charts, volume profile analysis
- **VectorBT Pro Integration**: Signal generation, backtesting, portfolio simulation, parameter optimization
- **Multi-Timeframe Support**: Tick, minute, and higher timeframe analysis
- **GPU Acceleration**: PyTorch MPS backend enabled for Apple Silicon (torch 2.12.0)
- **Numba-Optimized Indicators**: Fast computation of 500+ built-in and TA-Lib indicators
- **Risk Management**: Stop orders, cross-validation, deflated Sharpe ratio analysis

## Tech Stack

- **Python 3.11+** via Conda/Mamba
- **VectorBT Pro** — backtesting and portfolio simulation
- **PyTorch 2.12.0** — ML with MPS GPU support
- **TA-Lib** — technical analysis indicators
- **Numba** — JIT compilation for performance
- **direnv** — automatic environment activation
- **uv** — fast Python package management

## Installation

### Prerequisites

- macOS (Apple Silicon recommended for MPS GPU)
- [miniforge3](https://github.com/conda-forge/miniforge) (or Anaconda/Miniconda)
- [direnv](https://direnv.net/)
- [uv](https://docs.astral.sh/uv/)

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/davidleconte/OrderFlowOnFutures.git
cd OrderFlowOnFutures

# 2. Create conda environment (uses local .conda/ directory)
conda create --prefix .conda python=3.11

# 3. Install dependencies
uv pip install vectorbtpro torch torchvision

# 4. Install TA-Lib (macOS)
brew install ta-lib
pip install TA-Lib

# 5. Configure direnv for auto-activation
cp .envrc .envrc.backup  # if you want to keep a copy
# Edit .envrc paths if your conda base differs from /Users/david.leconte/miniforge3
direnv allow
```

### Environment Variables

The `.envrc` file automatically:
- Activates the local conda environment
- Sets `KMP_DUPLICATE_LIB_OK=TRUE` to resolve torch/libomp conflicts on macOS ARM

## Usage

```python
import vectorbtpro as vbt

# Load futures data
data = vbt.YFData.download('ES=F', start='2024-01-01')

# Generate signals
short_ma = vbt.MA.run(data.get('Close'), 10)
long_ma = vbt.MA.run(data.get('Close'), 50)
entries = short_ma.ma_crossed_above(long_ma)
exits = short_ma.ma_crossed_below(long_ma)

# Backtest
portfolio = vbt.Portfolio.from_signals(
    data.get('Close'),
    entries,
    exits,
    init_cash=100_000,
    fees=0.0001
)

print(portfolio.stats())
```

## Development

### Project Structure

```
OrderFlowOnFutures/
├── .envrc                 # direnv auto-activation
├── .gitignore             # Git ignore rules
├── .conda/                # Local conda environment (gitignored)
├── LICENSE                # MIT License
├── README.md              # This file
├── CONTRIBUTING.md        # Development guidelines
└── .github/
    └── workflows/
        └── python-ci.yml  # CI/CD pipeline
```

### Running Tests

```bash
# Activate environment (direnv should auto-activate)
direnv allow

# Run the test suite
python -m pytest tests/ -v
```

### Linting

```bash
ruff check .
ruff format --check .
```

## CI/CD

GitHub Actions workflows are configured to run on every push and pull request:

- **Linting**: `ruff` for code style and quality checks
- **Testing**: `pytest` for unit and integration tests
- **Build Validation**: Ensures the conda environment spec is valid

## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:

- Setting up the development environment
- Code style and linting standards
- Testing requirements
- Pull request process

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [VectorBT Pro](https://vectorbt.pro/) for the backtesting framework
- [PyPortfolioOpt](https://github.com/robertmartin8/PyPortfolioOpt) for portfolio optimization
- [Riskfolio-Lib](https://github.com/dcajasn/Riskfolio-Lib) for risk management
