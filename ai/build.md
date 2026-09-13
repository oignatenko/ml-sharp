# SHARP — Build Guide

## 1. Prerequisites

- **Python**: 3.13 (recommended)
- **GPU**: CUDA-capable GPU required for rendering; CPU/MPS supported for prediction only
- **OS**: Windows, Linux, macOS
- **Disk space**: ~5–10 GB (PyTorch + model checkpoint)

---

## 2. Quick Start

```bash
# 1. Create virtual environment
conda create -n sharp python=3.13
conda activate sharp

# 2. Install dependencies
pip install -r requirements.txt

# 3. Verify installation
sharp --help
```

---

## 3. Build from Source

### 3.1 Clone and Install

```bash
# Clone the repository
git clone https://github.com/apple/ml-sharp.git
cd ml-sharp

# Install in editable mode (recommended for development)
pip install -e .

# Or install in non-editable mode
pip install .
```

### 3.2 Using uv (faster dependency resolution)

```bash
# Install uv if not already installed
pip install uv

# Resolve and install dependencies
uv pip compile requirements.in -o requirements.txt --universal
uv pip install -r requirements.txt

# Install package in editable mode
uv pip install -e .
```

---

## 4. Development Setup

### 4.1 Pre-commit Hooks

```bash
# Install pre-commit
pip install pre-commit
pre-commit install
```

This configures three hooks (from `.pre-commit-config.yaml`):

| Hook | Tool | Purpose |
|------|------|---------|
| `trailing-whitespace` | pre-commit-hooks | Removes trailing whitespace |
| `end-of-file-fixer` | pre-commit-hooks | Ensures files end with newline |
| `ruff` + `ruff-format` | Ruff v0.1.7 | Linting + formatting |
| `mypy` | MyPy v1.7.1 | Static type checking |

### 4.2 Linting and Formatting

```bash
# Format code
ruff format .

# Lint and auto-fix
ruff check --fix .

# Run mypy type checking
mypy src/sharp
```

### 4.3 Type Checking with Pyright

```bash
pip install pyright
pyright src/sharp
```

---

## 5. Running the Model

### 5.1 Prediction (Image → PLY)

```bash
# Single image
sharp predict -i path/to/image.jpg -o output/

# Directory of images
sharp predict -i path/to/images/ -o output/

# Custom checkpoint
sharp predict -i image.jpg -o output/ -c path/to/checkpoint.pt

# With rendering (requires CUDA)
sharp predict -i image.jpg -o output/ --render

# Verbose logging
sharp predict -i image.jpg -o output/ -v
```

### 5.2 Rendering (PLY → Video)

```bash
# From a single PLY file
sharp render -i output/scene.ply -o renderings/

# From a directory
sharp render -i output/ -o renderings/
```

### 5.3 Model Checkpoint

The model is auto-downloaded on first run:
- **URL**: `https://ml-site.cdn-apple.com/models/sharp/sharp_2572gikvuh.pt`
- **Cache**: `~/.cache/torch/hub/checkpoints/`

Manual download:
```bash
wget https://ml-site.cdn-apple.com/models/sharp/sharp_2572gikvuh.pt
```

---

## 6. Project Structure

```
ml-sharp/
├── pyproject.toml           # Project metadata, dependencies, tool configs
├── requirements.in          # Direct dependencies
├── requirements.txt         # Pinned dependencies (auto-generated)
├── .pre-commit-config.yaml  # Pre-commit hooks
├── src/sharp/               # Main package
│   ├── cli/                 # CLI commands (predict, render)
│   ├── models/              # Neural network architecture
│   │   ├── encoders/        # SPN, ViT encoders
│   │   ├── decoders/        # DPT-style decoder
│   │   └── presets/         # Hyperparameter presets
│   └── utils/               # Utilities (gaussians, camera, IO, etc.)
└── data/                    # Sample assets
```

---

## 7. Dependencies

### Runtime

| Package | Purpose |
|---------|---------|
| `torch` (2.8.0) | Deep learning framework |
| `torchvision` (0.23.0) | Image utilities |
| `timm` (1.0.20) | Pre-trained ViT models (DINOv2) |
| `gsplat` (1.5.3) | 3D Gaussian Splatting renderer |
| `click` (8.3.0) | CLI framework |
| `plyfile` (1.1.2) | PLY file I/O |
| `scipy` (1.16.2) | Scientific computing |
| `matplotlib` (3.10.6) | Visualization |
| `imageio[ffmpeg]` (2.37.0) | Image/video I/O |
| `pillow-heif` (1.1.1) | HEIC image support |

### Build / Dev

| Package | Purpose |
|---------|---------|
| `setuptools` + `setuptools-scm` | Build system |
| `ruff` | Linting and formatting |
| `pyright` | Type checking |
| `pytest` (≥6.0) | Testing |
| `mypy` | Static type checking |
| `pre-commit` | Git hooks |

---

## 8. Environment Variables

No special environment variables are required. The project uses sensible defaults:

- **Device detection**: Auto-selects CUDA > MPS > CPU
- **Model cache**: `~/.cache/torch/hub/checkpoints/`
- **Logging**: INFO level by default, DEBUG with `-v`

---

## 9. Troubleshooting

### CUDA not found during rendering
```bash
# Verify CUDA availability
python -c "import torch; print(torch.cuda.is_available())"

# If CUDA is available but gsplat fails, reinstall gsplat
pip uninstall gsplat && pip install gsplat
```

### Out of memory
- Prediction: Use `--device cpu` (slower, lower memory)
- Rendering: Reduce scene complexity or use a GPU with more VRAM

### HEIC images fail to load
```bash
# Ensure pillow-heif is installed
pip install pillow-heif
```

### Model download fails
```bash
# Manual download
wget https://ml-site.cdn-apple.com/models/sharp/sharp_2572gikvuh.pt
sharp predict -i image.jpg -o output/ -c sharp_2572gikvuh.pt
```

### pyright / mypy errors
```bash
# Check types
pyright src/sharp
mypy src/sharp
```

---

## 10. Open Questions

Следующие вопросы остаются открытыми и требуют уточнения:

### 10.1 Тестирование

- **Отсутствует директория `tests/`** — в `pyproject.toml` указано `testpaths = ["tests"]`, но директория `tests/` не найдена в репозитории. Нужно уточнить:
  - Есть ли тесты в приватной части кодовой базы?
  - Нужен ли набор тестов для open-source версии?
  - Какая тестовая инфраструктура ожидается (pytest, unittest)?

### 10.2 Сборка для дистрибуции

- **Отсутствует `MANIFEST.in`** — неясно, какие не-Python файлы должны включаться в sdist/wheel (например, LICENSE, LICENSE_MODEL).
- **Не настроен CI/CD** — нет `.github/workflows/` или других CI-конфигураций. Нужно уточнить:
  - Нужен ли пайплайн для автоматической сборки wheel?
  - Требуется ли публикация в PyPI или внутренний индекс пакетов?
  - Нужен ли Dockerfile для контейнеризации?

### 10.3 Зависимости

- **gsplat требует CUDA** — в `requirements.txt` gsplat включён без условных зависимостей. Нужно уточнить:
  - Должна ли сборка работать на системах без CUDA (только prediction)?
  - Следует ли вынести `gsplat` в optional dependency (`[cuda]`)?
- **triton** установлен только для Linux x86_64 — поведение на других платформах (ARM, macOS) не протестировано явно.

### 10.4 Конфигурация линтинга

- **Ruff v0.1.7** — версия в `.pre-commit-config.yaml` значительно устарела (актуальная версия >0.9.x). Нужно уточнить:
  - Намеренно ли зафиксирована старая версия?
  - Следует ли обновить до последней стабильной версии?
- **mypy не имеет `mypy.ini` или настройки в `pyproject.toml`** — поведение type checking по умолчанию может быть недостаточным. Нужно уточнить:
  - Нужна ли строгая конфигурация mypy (strict mode, warn_return_any и т.д.)?

### 10.5 Модель и лицензирование

- **CHECKPOINT_MODEL_LICENSE** — файл `LICENSE_MODEL` существует, но его содержимое не изучено. Нужно уточнить:
  - Какие ограничения на商用 использование модели?
  - Требуется ли отдельная регистрация для получения checkpoint?
- **Checkpoint auto-download** — URL модели `https://ml-site.cdn-apple.com/models/sharp/sharp_2572gikvuh.pt` может быть недоступен из некоторых регионов. Нужно уточнить:
  - Есть ли зеркало или альтернативный источник?
  - Следует ли документировать процедуру получения checkpoint?

### 10.6 Документация

- **Отсутствует `docs/` директория** — полная документация API отсутствует. Нужно уточнить:
  - Планируется ли генерация API-документации (Sphinx, pdoc)?
  - Нужны ли примеры кода на Python (не только CLI)?
