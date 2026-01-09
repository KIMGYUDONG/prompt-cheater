# Contributing to prompt-cheater

Thank you for your interest in contributing to prompt-cheater!

## How to Contribute

### Reporting Bugs

1. Check if the bug has already been reported in [Issues](https://github.com/kimgyudong/prompt-cheater/issues)
2. If not, create a new issue with:
   - Clear title and description
   - Steps to reproduce
   - Expected vs actual behavior
   - Your environment (OS, Python version, Tmux version)

### Suggesting Features

1. Open a new issue with the `enhancement` label
2. Describe the feature and its use case
3. Explain why it would benefit other users

### Pull Requests

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes
4. Run tests and linting (see below)
5. Commit with clear messages
6. Push and create a Pull Request

## Development Setup

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/prompt-cheater.git
cd prompt-cheater

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -e ".[dev]"
# or with poetry
poetry install

# Install pre-commit hooks
pre-commit install
```

## Code Style

We use the following tools to maintain code quality:

```bash
# Linting
ruff check prompt_cheater/

# Formatting
ruff format prompt_cheater/

# Type checking
mypy prompt_cheater/

# Security check
bandit -r prompt_cheater/ -c pyproject.toml
```

All checks must pass before your PR can be merged.

## Running Tests

```bash
# Run tests
pytest

# Run tests with coverage
pytest --cov=prompt_cheater
```

## Commit Messages

- Use clear, descriptive commit messages
- Start with a verb: "Add", "Fix", "Update", "Remove"
- Keep the first line under 50 characters
- Add details in the body if needed

Example:
```
Add clipboard support for non-Tmux environments

- Add -c/--clipboard flag
- Use pyperclip for cross-platform support
- Update README with new option
```

## Code of Conduct

- Be respectful and inclusive
- Welcome newcomers
- Focus on constructive feedback
- No harassment or discrimination

## Questions?

Feel free to open an issue or reach out to the maintainers.

Thank you for contributing!
