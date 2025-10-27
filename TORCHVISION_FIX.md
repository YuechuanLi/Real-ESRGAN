# TorchVision Compatibility Fix

## Issue

When running Real-ESRGAN with Python 3.12 and recent versions of PyTorch/TorchVision, you may encounter:

```
ModuleNotFoundError: No module named 'torchvision.transforms.functional_tensor'
```

## Root Cause

The `basicsr` package (v1.4.2) imports `rgb_to_grayscale` from `torchvision.transforms.functional_tensor`, but newer versions of torchvision (v0.20+) have moved this function to `torchvision.transforms.functional`.

## Solution Applied

A compatibility patch was applied to the following file in your virtual environment:

**File:** `.venv/lib/python3.12/site-packages/basicsr/data/degradations.py`

**Original Code (Line 8):**
```python
from torchvision.transforms.functional_tensor import rgb_to_grayscale
```

**Fixed Code:**
```python
try:
    from torchvision.transforms.functional_tensor import rgb_to_grayscale
except ImportError:
    # Newer versions of torchvision moved this function
    from torchvision.transforms.functional import rgb_to_grayscale
```

## Why This Fix Works

The fix uses a try-except block to:
1. First attempt to import from the old location (for older torchvision versions)
2. Fall back to the new location if the import fails (for newer torchvision versions)

This makes the code compatible with both old and new versions of torchvision.

## Important Notes

⚠️ **This fix is applied to the installed package** in your virtual environment (`.venv`).

### If you recreate your virtual environment:

You'll need to reapply this fix. You can do this with:

```bash
# Method 1: Edit the file manually
nano .venv/lib/python3.12/site-packages/basicsr/data/degradations.py

# Method 2: Use sed to apply the fix automatically
sed -i '8s|from torchvision.transforms.functional_tensor import rgb_to_grayscale|try:\n    from torchvision.transforms.functional_tensor import rgb_to_grayscale\nexcept ImportError:\n    # Newer versions of torchvision moved this function\n    from torchvision.transforms.functional import rgb_to_grayscale|' .venv/lib/python3.12/site-packages/basicsr/data/degradations.py
```

### Alternative Solution

Wait for `basicsr` to release a newer version that's compatible with modern torchvision, or use an older version of torchvision:

```bash
# Not recommended, but you could downgrade torchvision
uv pip install "torchvision<0.20"
```

## Verification

The fix has been tested and confirmed working:

```bash
$ uv run python inference_realesrgan.py --help
# ✓ Works - no import errors

$ uv run python inference_realesrgan.py -n RealESRGAN_x4plus -i tests/data/lq/baboon.png --face_enhance
# ✓ Works - script runs successfully
```

## Current Environment

- Python: 3.12.12
- PyTorch: 2.9.0
- TorchVision: 0.24.0
- BasicSR: 1.4.2
- GFPGAN: 1.3.8
- FaceXLib: 0.3.0

## Related Issues

- [Real-ESRGAN Issue #858](https://github.com/xinntao/Real-ESRGAN/pull/858)
- [Real-ESRGAN Issue #859](https://github.com/xinntao/Real-ESRGAN/issues/859)

## Upstream Fix

This issue is known in the community. Future versions of `basicsr` or related packages may include this fix.
