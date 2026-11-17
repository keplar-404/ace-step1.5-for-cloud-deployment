# ACE-Step 1.5 - Music Generation

AI-powered music generation from text descriptions. Generate full songs with vocals, instrumentals, covers, and more.
### All credit goes to the main repo [ace-step-1.5](https://github.com/ace-step/ACE-Step-1.5.git). This repo is for easily deploy the model on the cloud for production usages.

## 🎵 What Can It Do?

- **Text-to-Music**: Generate complete songs from descriptions
- **Lyrics Support**: Create music with custom lyrics or auto-generate them
- **Audio Covers**: Transform existing audio into new styles
- **Audio Editing**: Repaint/regenerate specific time segments
- **Multi-track**: Generate specific instruments (Lego mode)
- **Source Separation**: Extract individual instruments from mixes
- **Track Completion**: Extend and complete partial tracks

## 💻 Platform Support

### Primary Target: Linux with NVIDIA GPUs (CUDA)

**Optimized for cloud deployment:**
- ✅ Thunder Compute, AWS, Vast.ai, RunPod, etc.
- ✅ Tested on RTX 3090, RTX A6000, A100
- ✅ Full acceleration with CUDA 11.8+
- ✅ Quantization, torch.compile, vLLM backend

### Secondary Support: macOS (Apple Silicon)

**Functional but not optimized:**
- ⚠️ Falls back to PyTorch MPS (Metal Performance Shaders)
- ⚠️ MLX acceleration removed in this fork
- ⚠️ Suitable for local testing only
- ⚠️ Slower than Linux/CUDA

**Note:** This fork is specifically optimized for cloud GPU inference on Linux. For maximum performance on Apple Silicon, use the [original ACE-Step 1.5 repository](https://github.com/ace-step/ACE-Step-1.5) which includes full MLX support.

## 🚀 Quick Start

### Prerequisites

- **GPU**: NVIDIA GPU with 8GB+ VRAM (16GB+ recommended)
- **OS**: Linux or macOS
- **CUDA**: 11.8+ (for NVIDIA GPUs)
- **Python**: 3.11

### Installation

#### 1. Clone the Repository

```bash
git clone https://github.com/keplar-404/ace-step1.5.git
cd ace-step1.5
```

#### 2. Install UV Package Manager

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

After installation, add to PATH:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

#### 3. Install Dependencies

```bash
uv sync
```

This will:
- Create a virtual environment in `.venv/`
- Install all Python dependencies (~140 packages)
- Download PyTorch with CUDA support

**Note**: First run will take 5-10 minutes to download and install everything.

### First Run

#### Interactive CLI

```bash
uv run python cli.py
```

The wizard will guide you through:
1. Choosing a task type (text2music, cover, etc.)
2. Entering music description/lyrics
3. Configuring generation parameters

**Models will auto-download** on first run (~9-10GB from HuggingFace).

#### Quick Example - Generate Instrumental Music

```bash
# Start the CLI
uv run python cli.py

# Follow the prompts:
# 1. Choose task: 1 (text2music)
# 2. Simple mode: n
# 3. Description: upbeat electronic dance music
# 4. Lyrics: 1 (instrumental)
# 5. Number of outputs: 1
# 6. Advanced params: n
# 7. Start generation: y
```

Output will be saved to `output/` directory as FLAC files.

## 📋 Usage Guide

### Generate Music from Text

```bash
uv run python cli.py
```

Example prompts:
- "upbeat electronic dance music"
- "slow blues guitar with harmonica"
- "orchestral cinematic epic soundtrack"
- "lo-fi hip hop beats for studying"

### Generate with Lyrics

1. Start CLI: `uv run python cli.py`
2. Choose task: `1` (text2music)
3. Enter description: "pop ballad with emotional vocals"
4. Lyrics option: `4` (paste directly) or `3` (from file)
5. Paste your lyrics or provide file path

### Batch Generation

Generate multiple variations:

```bash
uv run python generate_examples.py --num 5
```

### Using Configuration Files

Save your settings:

```bash
# First run creates config.toml
uv run python cli.py

# Reuse settings
uv run python cli.py -c config.toml
```

## 📦 Advanced Models

### Available Models

The system comes with default models, but you can download higher-quality alternatives:

#### List All Available Models

```bash
uv run python -m acestep.model_downloader --list
```

#### Model Comparison

| Model Type | Name | Size | Quality | Speed | Use Case |
|------------|------|------|---------|-------|----------|
| **LM Models** (Language/Metadata Generation) |
| LM 0.6B | `acestep-5Hz-lm-0.6B` | 1.2GB | Good | Fast | Quick tests |
| LM 1.7B | `acestep-5Hz-lm-1.7B` | 3.7GB | Better | Medium | Default ✓ |
| **LM 4B** | `acestep-5Hz-lm-4B` | 8GB | **Best** | Slower | **Highest quality** ⭐ |
| **DiT Models** (Audio Generation) |
| Turbo | `acestep-v15-turbo` | 4.8GB | Good | **Fastest** | Default ✓ |
| **SFT** | `acestep-v15-sft` | ~5GB | **Better** | Medium | **Higher quality** ⭐ |
| Base | `acestep-v15-base` | ~5GB | Good | Slow | Research |
| Turbo Shift1 | `acestep-v15-turbo-shift1` | ~5GB | Good | Fast | Alternative |
| Turbo Shift3 | `acestep-v15-turbo-shift3` | ~5GB | Good | Fast | Alternative |

### Download Advanced Models

#### Recommended: Best Quality Setup

For the highest quality output (recommended for RTX 3090/A6000/A100):

```bash
# Download 4B LM model (best quality lyrics/metadata)
uv run python -m acestep.model_downloader --model acestep-5Hz-lm-4B

# Download SFT DiT model (best quality audio)
uv run python -m acestep.model_downloader --model acestep-v15-sft
```

#### Download All Models

Download everything at once (~20GB total):

```bash
uv run python -m acestep.model_downloader --all
```

#### Download Specific Model

```bash
# Download any specific model
uv run python -m acestep.model_downloader --model <model-name>

# Examples:
uv run python -m acestep.model_downloader --model acestep-5Hz-lm-4B
uv run python -m acestep.model_downloader --model acestep-v15-sft
```

### Using Advanced Models

After downloading, select them in the CLI:

```bash
uv run python cli.py
```

**During the wizard:**
1. Choose task type (e.g., `1` for text2music)
2. **Select DiT model**: Choose `acestep-v15-sft` (or type the name)
3. **Select LM model**: Choose `acestep-5Hz-lm-4B` (or type the name)
4. Continue with your prompts

**Example session with best quality models:**
```
--- Available DiT Models ---
1. acestep-v15-turbo
2. acestep-v15-sft
Choose a model (number or name) [default: auto]: 2

--- Available LM Models ---
1. acestep-5Hz-lm-1.7B
2. acestep-5Hz-lm-4B
Choose a model (number or name) [default: auto]: 2
```

### Force Re-download

If you need to re-download a model:

```bash
uv run python -m acestep.model_downloader --model <model-name> --force
```

### Custom Download Location

```bash
uv run python -m acestep.model_downloader --model <model-name> --dir /path/to/checkpoints
```

## 🎨 LoRA Customization

**LoRA (Low-Rank Adaptation)** allows you to customize music generation by loading pre-trained adapters without modifying the base model.

### Use Cases

- 🎸 **Style customization** (rock, jazz, electronic, etc.)
- 🎤 **Artist emulation** (specific artist's sound)
- 🎵 **Genre specialization** (focus on specific genres)
- ⚙️ **Personalization** (your own fine-tuned adapters)

### Quick Example

```python
from acestep.handler import AceStepHandler
from acestep.inference import generate_music, GenerationParams

# Initialize handler
handler = AceStepHandler()
handler.initialize_service(
    checkpoint_dir="checkpoints",
    dit_model_path="acestep-v15-turbo",
    lm_model_path="acestep-5Hz-lm-1.7B",
)

# Load LoRA adapter
handler.load_lora(
    lora_path="checkpoints/lora/electronic_music",
    scale=0.8  # 0.0 to 1.0 (strength)
)

# Generate with LoRA
params = GenerationParams(
    caption="energetic techno with heavy bass",
    duration=30
)
result = generate_music(handler, params)
```

### LoRA Adapter Structure

Place LoRA adapters in `checkpoints/lora/`:

```
checkpoints/
├── lora/
│   ├── rock_style/
│   │   └── adapter_model.safetensors
│   ├── jazz_adapter/
│   │   └── adapter_model.safetensors
│   └── electronic_music/
│       └── adapter_model.safetensors
```

### Finding LoRA Adapters

1. **Train your own** - Use the [original ACE-Step 1.5 repo](https://github.com/ace-step/ACE-Step-1.5) with training code
2. **Community sharing** - Check HuggingFace for shared adapters
3. **Custom fine-tuning** - Fine-tune on your music dataset

**📖 For detailed LoRA usage, see [docs/LORA_USAGE.md](docs/LORA_USAGE.md)**


## 🛠️ Advanced Configuration

### Environment Variables

Create `.env` file (copy from `.env.example`):

```bash
cp .env.example .env
```

Edit `.env` to configure:
- Model paths
- GPU settings
- API endpoints
- Cache directories

### GPU Memory Tiers

The system auto-detects your GPU and configures limits:

| VRAM | Tier | Max Duration | Max Batch |
|------|------|--------------|-----------|
| 8GB  | Low  | 60s          | 1         |
| 16GB | Mid  | 120s         | 2         |
| 24GB | High | 180s         | 4         |
| 32GB+ | Unlimited | 600s    | 8         |

### Custom Duration

Specify duration when generating:

```bash
# In CLI, choose advanced parameters (y)
# Set duration: 30 (for 30 seconds)
```

## 📁 Output Files

Generated audio is saved to `output/` directory:

```
output/
├── <uuid>.flac          # Generated audio (FLAC format, 44.1kHz)
└── <uuid>_metadata.json # Generation parameters (optional)
```

## 🌐 API Server

This project includes a production-ready, persistent API server that supports **100% of the CLI features**, including advanced DiT parameters, LoRA management, and LLM reasoning.

### Key Features
- **Persistent Models**: Loads models once at startup (no reloading per request).
- **Full Control**: Supports Text-to-Music, Cover, Repaint, Extract, Lego, and Complete tasks via JSON.
- **Safety**: Built-in VRAM management and batch reduction.
- **Hot-Swapping**: Reload models or load LoRAs dynamically.

### Installation & Running
The server requires `fastapi` and `uvicorn`, which are included in the project dependencies.

1.  **Install Dependencies**:
    ```bash
    uv sync
    ```

2.  **Start the Server**:
    ```bash
    ./run_server.sh
    ```

3.  **Documentation**:
    For detailed usage, JSON examples, and configuration options, see the **[API Server Guide](API_README.md)**.

## 🐛 Troubleshooting

### Models Not Downloading

If models fail to download automatically:

```bash
# Check internet connection
# Verify HuggingFace access
# Check disk space (need ~15GB free)
```

### CUDA/GPU Issues

```bash
# Verify CUDA installation
nvidia-smi

# Check PyTorch CUDA support
uv run python -c "import torch; print(torch.cuda.is_available())"
```

### Out of Memory Errors

- Reduce batch size to 1
- Generate shorter durations
- Close other GPU applications
- Use tiled VAE decoding (enabled by default)

### Module Import Errors

```bash
# Reinstall dependencies
uv sync --reinstall
```

## 🔧 Model Management

### Downloaded Models Location

Models are stored in `checkpoints/`:

```
checkpoints/
├── acestep-v15-turbo/           # Main DiT model (4.79GB)
├── acestep-5Hz-lm-1.7B/         # Language model (3.71GB)
├── vae/                         # VAE decoder (337MB)
└── Qwen3-Embedding-0.6B/        # Text encoder (1.19GB)
```

### Managing Disk Space

```bash
# Check model sizes
du -sh checkpoints/*

# Remove downloaded models (will re-download on next use)
rm -rf checkpoints/
```

## 🌐 Cloud GPU Setup

### Thunder Compute (Recommended)

```bash
# Install Thunder CLI
curl https://thunder.softwar.ai/install.sh | sh

# Create instance
tnr launch --gpu-type=nvidia-rtx-a6000 --gpu-count=1

# Connect
tnr connect 0

# On cloud instance:
git clone https://github.com/keplar-404/ace-step1.5.git
cd ace-step1.5
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="/home/ubuntu/.local/bin:$PATH"
uv sync
uv run python cli.py
```

### Other Cloud Providers

Works on any cloud GPU instance with:
- NVIDIA GPU (T4, A10, A100, RTX series)
- Ubuntu 20.04+ or similar Linux
- CUDA 11.8+

## 📊 Performance

Typical generation times on various GPUs:

| GPU | VRAM | 30s Song | 60s Song |
|-----|------|----------|----------|
| RTX 3060 | 12GB | ~45s | ~90s |
| RTX 3090 | 24GB | ~35s | ~70s |
| RTX A6000 | 48GB | ~40s | ~80s |
| A100 | 80GB | ~30s | ~60s |

*Times include LM + DiT + VAE processing*

## 🤝 Contributing

This is a customized version of the original ACE-Step 1.5 project, optimized for cloud deployment and inference.

### Changes from Original

- Removed training code
- Removed UI/Gradio interface
- Removed platform-specific launchers
- Fixed MLX import compatibility
- Streamlined CLI for direct usage

## 🏆 Credits

**This project is heavily based on [ACE-Step 1.5](https://github.com/ace-step/ACE-Step-1.5)**.

All credit for the core model architecture, training, and research goes to the original authors. This repository merely provides a streamlined interface for inference and cloud deployment.

**Original Repository**: [https://github.com/ace-step/ACE-Step-1.5](https://github.com/ace-step/ACE-Step-1.5)

## 📝 License

See `LICENSE` file for details.

## 🔗 Links

- **Original ACE-Step 1.5 Repository**: [https://github.com/ace-step/ACE-Step-1.5](https://github.com/ace-step/ACE-Step-1.5)
- **This Fork**: [keplar-404/ace-step1.5](https://github.com/keplar-404/ace-step1.5)

## ⚠️ Known Issues

1. **VLLM Backend**: May fail with libcuda.so linking issues on some systems
   - **Impact**: Falls back to PyTorch (slightly slower, but fully functional)
   - **Fix**: Run `sudo /sbin/ldconfig` to refresh linker cache

2. **TorchAO Warning**: Version incompatibility with PyTorch 2.10+
   - **Impact**: None (cpp extensions skipped, pure Python fallback works)

## 💡 Tips

1. **First generation is slower** - Models are loaded into GPU memory
2. **Subsequent generations are faster** - Models stay loaded
3. **Use descriptive prompts** - More detail = better results
4. **Experiment with seeds** - Use `--seed` for reproducible results
5. **Save good configs** - Reuse with `-c config.toml`

## 🎯 Example Outputs

With the default settings, you can generate:
- 30-second songs in ~40-50s
- Professional FLAC audio quality
- Auto-generated metadata (BPM, key, etc.)
- Expandable captions for refined control

---

**Ready to create music with AI!** 🎵

Start with: `uv run python cli.py`
