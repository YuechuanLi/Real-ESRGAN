# Python Version Compatibility for Real-ESRGAN

## Supported Python Versions

Based on the dependencies (PyTorch, basicsr, gfpgan, facexlib), here are the compatible Python versions:

### ✅ Recommended Versions (Best Compatibility)

| Python Version | Status | Notes |
|----------------|--------|-------|
| **3.10** | ✅ **Recommended** | Best balance of stability and compatibility |
| **3.11** | ✅ **Recommended** | Good performance, well-supported |
| **3.12** | ✅ Supported | Currently used in this setup, works well |

### ⚠️ Other Supported Versions

| Python Version | Status | Notes |
|----------------|--------|-------|
| **3.8** | ⚠️ Minimum | Minimum required, nearing EOL (Oct 2024) |
| **3.9** | ✅ Supported | Stable and well-tested |

### ❌ Not Recommended

| Python Version | Status | Notes |
|----------------|--------|-------|
| **3.13+** | ❌ Not tested | PyTorch/NumPy may have limited support |
| **3.7** | ❌ Not supported | EOL, dependency conflicts |

## Current Setup

Currently configured for: **Python >= 3.8**
Currently using: **Python 3.12.12** (in .venv)

## How to Switch Python Versions with UV

### Option 1: Specify Python version when creating venv

```bash
# Remove existing virtual environment
rm -rf .venv

# Create venv with specific Python version
uv venv --python 3.10
# OR
uv venv --python 3.11
# OR
uv venv --python 3.12

# Install dependencies
uv sync
```

### Option 2: Use a specific Python version directly

```bash
# Let uv download and use Python 3.11
uv venv --python 3.11

# Let uv download and use Python 3.10
uv venv --python 3.10

# UV will automatically download the Python version if not installed
```

### Option 3: Use system Python

```bash
# Use system Python 3.10
uv venv --python /usr/bin/python3.10

# Use system Python 3.11
uv venv --python /usr/bin/python3.11
```

## Checking Available Versions

```bash
# List all Python versions available to uv
uv python list

# Install a specific Python version
uv python install 3.11

# Pin Python version for the project
uv python pin 3.11
```

## Dependency Requirements

### PyTorch
- Requires: Python >= 3.8
- Optimal: Python 3.9-3.12
- Latest PyTorch 2.x supports Python 3.8-3.12

### BasicSR
- Requires: Python >= 3.7 (but we use 3.8+ for other deps)

### GFPGAN & FaceXLib
- Requires: Python >= 3.7
- Works best with: Python 3.8-3.11

## Performance Considerations

| Python Version | Performance | Compatibility |
|----------------|-------------|---------------|
| 3.8 | Baseline | Good |
| 3.9 | +5-10% | Good |
| 3.10 | +10-15% | Excellent |
| 3.11 | +15-25% | Excellent |
| 3.12 | +20-30% | Very Good |

**Note:** Python 3.11+ includes significant performance improvements

## Our Recommendation

**For Production/Research:**
- Use **Python 3.10** or **Python 3.11** for best stability and performance

**For Latest Features:**
- Use **Python 3.12** (currently setup) - works well with latest PyTorch

**For Legacy Systems:**
- Use **Python 3.8** or **3.9** if needed for compatibility

## Quick Setup Examples

### Setup with Python 3.11 (Recommended)
```bash
rm -rf .venv
uv venv --python 3.11
uv sync
source .venv/bin/activate
```

### Setup with Python 3.10 (Most Stable)
```bash
rm -rf .venv
uv venv --python 3.10
uv sync
source .venv/bin/activate
```

### Setup with Python 3.12 (Current/Latest)
```bash
# Already set up! Just use:
source .venv/bin/activate
```

## Verifying Your Setup

After switching Python versions, verify everything works:

```bash
source .venv/bin/activate

# Check Python version
python --version

# Check PyTorch
python -c "import torch; print(f'PyTorch: {torch.__version__}')"

# Check CUDA (if using GPU)
python -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}')"

# Test realesrgan import
python -c "import realesrgan; print('Real-ESRGAN OK')"
```

## Troubleshooting

### If you get dependency conflicts:
```bash
# Clear cache and reinstall
uv cache clean
rm -rf .venv
uv venv --python 3.11
uv sync
```

### If PyTorch installation fails:
```bash
# Try with a specific Python version known to work
uv venv --python 3.10
uv sync
```

### Check what Python version uv is using:
```bash
uv python find
```
