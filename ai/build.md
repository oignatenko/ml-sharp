# SHARP — Build Guide

Описан процесс локальной сборки проекта от клонирования до запуска `sharp --help`. Всё, что выходит за эти рамки, считается out of scope.

---

## 1. Пререквизиты

- **Python**: 3.13
- **Дисковое пространство**: ~5–10 GB (PyTorch + checkpoint модели)
- **Git**: для клонирования репозитория
- **Пакетный менеджер**: `pip` (или `uv`)

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

## 3. Open Questions

### 3.1. Отсутствует директория `tests/`

В `pyproject.toml` указано `testpaths = ["tests"]`, но директория `tests/` не найдена в репозитории.

**Блокирует задачу**: нет. Сборка и запуск `sharp --help` выполняются без тестов.

### 3.2. Отсутствует `MANIFEST.in`

Неясно, какие не-Python файлы (LICENSE, LICENSE_MODEL) должны включаться в sdist/wheel.

**Блокирует задачу**: нет. Не влияет на локальную сборку через `pip install -e .`.
