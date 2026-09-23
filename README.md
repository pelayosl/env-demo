# env-demo

Proyecto pequeño para practicar la creación de un entorno reproducible con
`uv`, Git y Python. El mismo paquete se ejecuta en los entornos `dev`, `pre`
y `pro`; el comportamiento cambia únicamente mediante la variable `APP_ENV`.

## Requisitos

- Python 3.12 o superior
- `uv`
- Git

## Cómo se construyó

El proyecto se creó desde un directorio vacío con estos pasos:

```bash
mkdir env-demo
cd env-demo
git init
uv init --package --vcs none .
```

Después se creó `.gitignore` para no versionar el entorno virtual ni las
carpetas de caché, y se declararon las dependencias:

```bash
uv add rich
uv add --dev pytest ruff
```

La aplicación se implementó en `src/env_demo/main.py`. `environment_message`
normaliza el valor recibido, acepta `dev`, `pre` y `pro`, y lanza `ValueError`
para cualquier otro valor. El comando principal lee `APP_ENV` y muestra el
entorno activo mediante Rich.

Los casos del contrato se añadieron en `tests/test_main.py`: se prueban los
tres entornos válidos y el error para `local`.

## Reproducir desde un checkout limpio

Desde la carpeta del proyecto, sincroniza las dependencias usando el `uv.lock`
versionado:

```bash
uv sync --dev
```

Esto crea o actualiza el único `.venv` del proyecto. No es necesario crear
entornos separados para `dev`, `pre` y `pro`.

### Ejecutar en Bash

```bash
APP_ENV=dev uv run python -m env_demo.main
APP_ENV=pre uv run python -m env_demo.main
APP_ENV=pro uv run python -m env_demo.main
```

### Ejecutar en PowerShell

```powershell
$env:APP_ENV = "dev"; uv run python -m env_demo.main
$env:APP_ENV = "pre"; uv run python -m env_demo.main
$env:APP_ENV = "pro"; uv run python -m env_demo.main
```

El resultado esperado es un mensaje con `DEV`, `PRE` o `PRO`. Un valor no
admitido produce un `ValueError` indicando que `APP_ENV` debe ser `dev`, `pre`
o `pro`.

## Comprobar el proyecto

Ejecuta las pruebas y el análisis de Ruff con las herramientas del entorno:

```bash
uv run pytest
uv run ruff check .
```

Para comprobar la trazabilidad local de Git:

```bash
git log --oneline
git ls-files .venv
git status --short --ignored
```

`git ls-files .venv` no debe mostrar archivos. El historial contiene commits
locales para la estructura inicial, la implementación del comando, las
pruebas y la documentación; no se configura ningún remoto.

## Estructura

```text
env-demo/
├── pyproject.toml
├── uv.lock
├── .gitignore
├── src/
│   └── env_demo/
│       └── main.py
└── tests/
	└── test_main.py
```
