# Video Upscaling Guide for Real-ESRGAN

## Quick Start: 2x Video Upscaling

### Basic 2x Upscaling

```bash
# Using RealESRGAN_x2plus model (recommended for 2x)
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input_video.mp4 \
  -o output_folder

# The output video will be 2x the resolution of input
```

### Alternative: Using 4x Model with 2x Scaling

If you want to use the x4plus model but output at 2x:

```bash
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x4plus \
  -i input_video.mp4 \
  -o output_folder \
  -s 2

# The -s 2 flag will scale to 2x (even though the model is 4x)
```

## Model Options for Video

### For General Videos

| Model | Scale | Best For | Command Flag |
|-------|-------|----------|--------------|
| **RealESRGAN_x2plus** | 2x | General videos at 2x | `-n RealESRGAN_x2plus` |
| **RealESRGAN_x4plus** | 4x | General videos at 4x | `-n RealESRGAN_x4plus` |
| **RealESRNet_x4plus** | 4x | Smoother results | `-n RealESRNet_x4plus` |
| **realesr-general-x4v3** | 4x | Latest general model | `-n realesr-general-x4v3` |

### For Anime Videos

| Model | Scale | Best For | Command Flag |
|-------|-------|----------|--------------|
| **realesr-animevideov3** | 4x | Anime videos (default) | `-n realesr-animevideov3` |
| **RealESRGAN_x4plus_anime_6B** | 4x | Anime images/stills | `-n RealESRGAN_x4plus_anime_6B` |

## Complete Examples

### 1. Basic 2x Upscaling (General Video)

```bash
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input.mp4 \
  -o results
```

### 2. 2x Upscaling with Face Enhancement

```bash
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input.mp4 \
  -o results \
  --face_enhance
```

### 3. 2x Anime Video Upscaling

```bash
uv run python inference_realesrgan_video.py \
  -n realesr-animevideov3 \
  -i anime.mp4 \
  -o results \
  -s 2
```

### 4. Custom FPS and Tile Size (for VRAM constraints)

```bash
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input.mp4 \
  -o results \
  --fps 30 \
  -t 400
```

### 5. High Quality with FP32 Precision

```bash
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input.mp4 \
  -o results \
  --fp32
```

## Important Parameters

### `-s, --outscale` (Output Scale)
Control the final output scale:
- `-s 2` - 2x upscaling
- `-s 3` - 3x upscaling
- `-s 4` - 4x upscaling

**Note:** This will further resize the model output. For example, using `RealESRGAN_x4plus` with `-s 2` will upscale 4x then downscale to 2x.

### `-t, --tile` (Tile Size)
For videos that don't fit in VRAM:
- `-t 0` - No tiling (use full resolution)
- `-t 400` - Process in 400x400 tiles (recommended for 8GB VRAM)
- `-t 200` - Process in 200x200 tiles (for 4GB VRAM)

Smaller tiles = more VRAM friendly but may have slight artifacts at tile boundaries.

### `--fps` (Frames Per Second)
Set output video FPS:
```bash
--fps 30    # 30 fps output
--fps 60    # 60 fps output
```

### `--face_enhance`
Use GFPGAN to enhance faces in the video:
```bash
--face_enhance
```

### `--fp32`
Use full precision (slower but potentially better quality):
```bash
--fp32
```

## Prerequisites

### 1. System Requirements

**Required:**
- ffmpeg installed on your system

**Install ffmpeg:**
```bash
# Ubuntu/Debian
sudo apt install ffmpeg

# macOS
brew install ffmpeg

# Or download from: https://ffmpeg.org/download.html
```

### 2. Python Dependencies

Already included with UV setup:
```bash
uv sync  # This installs ffmpeg-python automatically
```

## Workflow Examples

### Example 1: Upscale 1080p to 4K (2x)

```bash
# Input: 1920x1080 video
# Output: 3840x2160 (4K)

uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i video_1080p.mp4 \
  -o results_4k \
  --fps 30
```

### Example 2: Upscale 720p to 1440p (2x)

```bash
# Input: 1280x720 video
# Output: 2560x1440

uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i video_720p.mp4 \
  -o results_1440p \
  -t 400
```

### Example 3: Upscale Old Anime (480p to 1080p ~2.25x)

```bash
# Input: 854x480 video
# Output: ~1920x1080

uv run python inference_realesrgan_video.py \
  -n realesr-animevideov3 \
  -i old_anime.mp4 \
  -o results \
  -s 2.25
```

## Performance Tips

### 1. GPU Memory Issues

If you run out of VRAM:
```bash
# Reduce tile size
-t 200

# Or use smaller batch
--num_process_per_gpu 1
```

### 2. Speed Up Processing

```bash
# Use fp16 (default, faster)
# Don't use --fp32

# Extract frames first (faster for multiple runs)
--extract_frame_first
```

### 3. Quality vs Speed Trade-off

| Setting | Speed | Quality |
|---------|-------|---------|
| `--fp32` | Slower | Best |
| Default (fp16) | Fast | Very Good |
| `-t 200` | Faster | Good (small tiles) |
| `-t 0` | Depends | Best (no tiles) |

## Output Location

By default:
- Output videos are saved to the `-o` folder
- Named with suffix `_out.mp4`
- Can customize suffix with `--suffix`

Example:
```bash
# Custom suffix
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input.mp4 \
  -o results \
  --suffix "_2x_upscaled"

# Output: results/input_2x_upscaled.mp4
```

## Troubleshooting

### Issue: "No module named 'ffmpeg'"

Solution:
```bash
uv add ffmpeg-python
```

### Issue: Out of GPU memory

Solutions:
1. Reduce tile size: `-t 200` or `-t 100`
2. Use smaller model: `RealESRGAN_x2plus` instead of `x4plus`
3. Process fewer frames per GPU: `--num_process_per_gpu 1`

### Issue: ffmpeg not found

Solution:
```bash
# Ubuntu/Debian
sudo apt install ffmpeg

# macOS
brew install ffmpeg

# Or specify path
--ffmpeg_bin /path/to/ffmpeg
```

### Issue: Video artifacts at tile boundaries

Solutions:
1. Increase tile padding: `--tile_pad 20`
2. Increase pre-padding: `--pre_pad 20`
3. Use larger tiles (if VRAM allows): `-t 800`

## Download Pre-trained Models

Models are automatically downloaded on first use. You can also download manually:

```bash
# RealESRGAN_x2plus (for 2x upscaling)
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.1/RealESRGAN_x2plus.pth -P weights

# RealESRGAN_x4plus (for 4x upscaling)
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth -P weights

# Anime video model
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.5.0/realesr-animevideov3.pth -P weights
```

## Batch Processing

Process multiple videos:

```bash
# Process all videos in a folder
uv run python inference_realesrgan_video.py \
  -n RealESRGAN_x2plus \
  -i input_folder/ \
  -o output_folder/
```

## Summary: Quick Commands

```bash
# Standard 2x upscaling
uv run python inference_realesrgan_video.py -n RealESRGAN_x2plus -i input.mp4 -o results

# 2x with face enhancement
uv run python inference_realesrgan_video.py -n RealESRGAN_x2plus -i input.mp4 -o results --face_enhance

# 2x anime
uv run python inference_realesrgan_video.py -n realesr-animevideov3 -i anime.mp4 -o results -s 2

# 2x with low VRAM
uv run python inference_realesrgan_video.py -n RealESRGAN_x2plus -i input.mp4 -o results -t 200
```

## More Information

- Model Zoo: [docs/model_zoo.md](docs/model_zoo.md)
- Training Guide: [docs/Training.md](docs/Training.md)
- Anime Model Details: [docs/anime_video_model.md](docs/anime_video_model.md)
