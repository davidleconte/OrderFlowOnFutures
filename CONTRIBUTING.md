# Contributing to OrderFlowOnFutures

Thank you for your interest in contributing to this research project! This document provides guidelines for development.

## Development Setup

### 1. Clone and Initialize

```bash
git clone https://github.com/davidleconte/OrderFlowOnFutures.git
cd OrderFlowOnFutures
direnv allow  # auto-activates conda environment
```

### 2. Install Dependencies

```bash
# Core packages
uv pip install vectorbtpro torch torchvision

# TA-Lib (requires homebrew package on macOS)
brew install ta-lib
pip install TA-Lib

# Dev tools
uv pip install ruff pytest pytest-cov
```

### 3. Verify Setup

```bash
python -c "import vectorbtpro; print(f'VectorBT Pro: {vectorbtpro.__version__}')"
python -c "import torch; print(f'PyTorch MPS available: {torch.backends.mps.is_available()}')"
```

## Code Style

We use **ruff** for linting and formatting:

```bash
# Check for linting issues
ruff check .

# Auto-fix issues
ruff check . --fix

# Format code
ruff format .
```

### Style Guidelines

- Follow PEP 8 conventions
- Use type hints for function signatures
- Maximum line length: 120 characters
- Docstrings required for all public functions and classes
- Import order: standard library → third-party → local (enforced by ruff)

## Testing

### Running Tests

```bash
# Run all tests
python -m pytest tests/ -v

# Run with coverage
python -m pytest tests/ -v --cov=. --cov-report=html

# Run specific test file
python -m pytest tests/test_orderflow.py -v
```

### Writing Tests

- Place tests in `tests/` directory
- Name test files `test_<module>.py`
- Use descriptive test function names: `test_<function>_<scenario>_<expected>`
- Include both positive and negative test cases
- Mock external data sources (Yahoo Finance, Binance) in unit tests

## Git Workflow

### Branch Naming

- `feature/<description>` — new features or strategies
- `fix/<description>` — bug fixes
- `research/<description>` — experimental analysis
- `docs/<description>` — documentation updates

### Commit Messages

Follow conventional commit format:

```
<type>(<scope>): <description>

feat(orderflow): add volume profile imbalance indicator
fix(backtest): correct slippage calculation for ES futures
docs(readme): update installation prerequisites
research: initial momentum strategy baseline
```

### Pull Requests

1. Ensure all tests pass locally
2. Run `ruff check .` and `ruff format .` — no violations
3. Update documentation if API or behavior changes
4. Reference related issues or research notes
5. Request review from at least one contributor

## Quant Research Guidelines

When contributing trading strategies or analysis:

### Required Artifacts

For any new strategy or backtest result, include:

1. **Manifest file** (`manifest.json`):
   - Parameters used
   - Data source and timeframe
   - Date ranges (in-sample, out-of-sample)
   - Performance metrics (Sharpe, Sortino, max drawdown)

2. **Null test**:
   - Run the same strategy on shuffled/randomized data
   - Verify that edge disappears (confirms no look-ahead bias)

3. **Deflated Sharpe Ratio**:
   - Calculate DSR to account for multiple testing bias
   - Report adjusted performance expectations

### Guardrails

- **No look-ahead bias**: Ensure signals only use data available at decision time
- **No overfitting**: Limit parameter combinations; use cross-validation
- **Out-of-sample validation**: Always test on unseen data
- **Transaction costs**: Include realistic fees and slippage
- **Survivorship bias**: Account for delisted instruments when applicable

## Adding New Strategies

1. Create strategy in `strategies/` directory
2. Add corresponding test in `tests/test_<strategy_name>.py`
3. Document parameters and expected inputs in docstrings
4. Include a null test and deflated Sharpe analysis
5. Add to strategy registry in `strategies/__init__.py`

## Reporting Issues

When filing issues:

- Include reproducible steps
- Provide environment details (Python version, package versions)
- Attach relevant logs or error messages
- For performance issues, include dataset size and hardware specs

## Questions?

Open a GitHub Discussion for research questions, or an Issue for bugs.
