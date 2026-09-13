# SHARP — Build Guide

Описан процесс сборки проекта от клонирования до запуска `sharp --help`. Всё, что выходит за эти рамки, считается out of scope.

---

## 1. Пререквизиты

- **Python**: 3.13
- **Дисковое пространство**: ~5–10 GB (PyTorch + checkpoint модели)
- **Git**: для клонирования репозитория

GPU не требуется для сборки и запуска `sharp --help`. CUDA нужен только для рендеринга (out of scope).

---

## 2. Сборка

### Шаг 1. Клонировать репозиторий

```bash
git clone https://github.com/apple/ml-sharp.git
cd ml-sharp
```

### Шаг 2. Создать виртуальное окружение

```bash
conda create -n sharp python=3.13
conda activate sharp
```

### Шаг 3. Установить зависимости

```bash
pip install -r requirements.txt
```

### Шаг 4. Установить пакет

```bash
pip install -e .
```

### Шаг 5. Проверить установку

```bash
sharp --help
```

---

## 3. Альтернативный способ (uv)

Если используется `uv` вместо `pip`:

```bash
pip install uv
uv pip compile requirements.in -o requirements.txt --universal
uv pip install -r requirements.txt
uv pip install -e .
sharp --help
```

---

## 4. Open Questions

- **Отсутствует директория `tests/`** — в `pyproject.toml` указано `testpaths = ["tests"]`, но директория `tests/` не найдена в репозитории.
- **Отсутствует `MANIFEST.in`** — неясно, какие не-Python файлы (LICENSE, LICENSE_MODEL) должны включаться в sdist/wheel.
- **Не настроен CI/CD** — нет `.github/workflows/` или других CI-конфигураций.
- **Ruff v0.1.7** в `.pre-commit-config.yaml` значительно устарел (актуальная версия >0.9.x).
- **mypy не имеет конфигурации** — нет `mypy.ini` или настроек в `pyproject.toml`.
