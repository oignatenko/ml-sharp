# SHARP — Build Guide

Описан процесс локальной сборки проекта от клонирования до запуска `sharp --help`. Всё, что выходит за эти рамки, считается out of scope.

---

## 0. Переход на Python 3.13

На этом компьютере установлен Python 3.10.5, а проект требует Python 3.13 (файл `.python-version`). Описание билда начинается с инструкции по переходу на Python 3.13.

### Шаг 0.1. Проверить наличие Python 3.13

```bash
py --list-paths
```

Если Python 3.13 отсутствует (доступен только Python 3.10), установить:

### Шаг 0.2. Установить Python 3.13 (если не установлен)

Через winget (требует администратор):

```bash
winget install --id Python.Python.3.13 --accept-package-agreements --accept-source-agreements
```

Установится в `C:\Users\<user>\AppData\Local\Programs\Python\Python313\`.

### Шаг 0.3. Убедиться, что `py -3.13` доступен

```bash
py -3.13 --version
# Expected output: Python 3.13.x
```

---

## 1. Пререквизиты

| Инструмент | Версия | Статус |
|------------|--------|--------|
| **Python** | 3.13 | ✅ требуется (установлен через winget) |
| **pip** | — | ✅ будет установлен вместе с Python |
| **git** | 2.39.1 | ✅ установлен |
| **conda** | — | ❌ не установлен |
| **uv** | — | ❌ не установлен |

> **Примечание**: `venv` доступен через `py -3.13 -m venv`.

---

## 2. Сборка

### Шаг 1. Клонировать репозиторий

```bash
git clone https://github.com/apple/ml-sharp.git
cd ml-sharp
```

### Шаг 2. Создать виртуальное окружение на Python 3.13

```bash
py -3.13 -m venv .venv
```

### Шаг 3. Установить зависимости

```bash
.venv\Scripts\python.exe -m pip install -r requirements.txt
```

### Шаг 4. Установить пакет

```bash
.venv\Scripts\python.exe -m pip install -e .
```

### Шаг 5. Проверить установку

```bash
.venv\Scripts\sharp.exe --help
```

Ожидаемый вывод:

```
Usage: sharp [OPTIONS] COMMAND [ARGS]...

  Run inference for SHARP model.

Options:
  --help  Show this message and exit.

Commands:
  predict  Predict Gaussians from input images.
  render   Predict Gaussians from input images.
```

---

## 3. Open Questions

### 3.1. Версия Python: 3.10 vs 3.13

На этом компьютере изначально был установлен Python 3.10.5, а проект требует Python 3.13 (файл `.python-version`).

**Блокирует задачу**: да. Зависимости (`contourpy`, `networkx`) могут не иметь wheel-пакетов для Python 3.10, и `pip install -r requirements.txt` завершится ошибкой. Требуется Python 3.13.
