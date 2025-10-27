# UV Setup for Real-ESRGAN

This project now supports [uv](https://github.com/astral-sh/uv), a fast Python package installer and resolver.

## Python Version Compatibility

This project supports **Python 3.8 - 3.12**.

**Recommended:** Python 3.10 or 3.11 for best compatibility and performance.

See [PYTHON_VERSIONS.md](PYTHON_VERSIONS.md) for detailed compatibility information and how to switch Python versions.

## Quick Start

### 1. Install uv (if not already installed)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or on macOS/Linux:
```bash
pip install uv
```

### 2. Create and activate virtual environment

```bash
# Create virtual environment
uv venv

# Activate the virtual environment
source .venv/bin/activate  # On Linux/macOS
# OR
.venv\Scripts\activate  # On Windows
```

### 3. Install dependencies

```bash
# Install production dependencies only
uv sync --no-dev

# OR install with dev dependencies (pytest, flake8, etc.)
uv sync
```

### 4. Install in editable mode for development

The package is automatically installed in editable mode when you run `uv sync`.

## Common Commands

```bash
# Add a new dependency
uv add package-name

# Add a dev dependency
uv add --dev package-name

# Remove a dependency
uv remove package-name

# Update all dependencies
uv sync --upgrade

# Run a command in the virtual environment
uv run python inference_realesrgan.py -n RealESRGAN_x4plus -i inputs

# Install from lock file (faster, reproducible)
uv sync --frozen
```

## Benefits of using uv

- **10-100x faster** than pip for dependency resolution and installation
- **Reproducible** environments with lock files
- **Better dependency resolution** - finds compatible versions faster
- **Built-in virtual environment** management
- **Drop-in replacement** for pip in most cases

## Migration from pip/conda

If you were previously using:

```bash
pip install -r requirements.txt
python setup.py develop
```

Now simply use:

```bash
uv sync
```

This will:
1. Create a virtual environment (if not exists)
2. Install all dependencies from `pyproject.toml`
3. Install the package in editable mode

## Project Structure

- `pyproject.toml` - Modern Python project configuration with dependencies
- `.venv/` - Virtual environment (created by `uv venv`)
- `uv.lock` - Lock file for reproducible installations (auto-generated)

## Troubleshooting

### Issue: "No solution found"

This usually means there's a dependency conflict. Try:
```bash
uv sync --resolution=lowest-direct
```

### Issue: Permission errors

Make sure you have write permissions to the project directory.

### Issue: Cache issues

Clear the uv cache:
```bash
uv cache clean
```

## Additional Resources

- [uv Documentation](https://docs.astral.sh/uv/)
- [pyproject.toml specification](https://packaging.python.org/en/latest/specifications/pyproject-toml/)
