# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este proyecto

Curso de pandas intermedio → avanzado en español, formado por 10 cuadernos Jupyter (`01_…` a `10_…`) que trabajan sobre una muestra del dataset *Olist Brazilian E-commerce*. No es un paquete: no hay build, tests ni linter. El "código" son las celdas de los cuadernos.

Es un repositorio git (rama `main`, sin remoto por ahora). `.gitignore` excluye `venv/`, `cache/`, `salida/` y `.ipynb_checkpoints/`. Hacer commits solo cuando el usuario lo pida.

`README.md` es el índice del curso para el alumno: módulos, subtemas de cada sección, ejercicios con su dificultad, instalación y descripción del dataset. Consultarlo para ubicar en qué módulo se trata un tema. Si se añade, renombra o reordena una sección o un ejercicio en un cuaderno, actualizar también su entrada en el README (índice y "Contenido detallado").

`verify.ipynb` es solo un chequeo del entorno (imprime el intérprete y las versiones de pandas/numpy/pyarrow).

## Entorno y comandos

- Python 3.12 en `venv/` (pandas 3.x obligatorio; el setup del módulo 01 hace `assert` de la versión mayor). Dependencias en `requirements.txt`.
- Usar siempre el intérprete del venv: `venv/bin/python`, `venv/bin/jupyter`.
- Ejecutar un cuaderno de forma headless **sin sobrescribir sus outputs** (el cwd debe ser la raíz del proyecto, porque las rutas son relativas a `data/`):
  ```bash
  venv/bin/jupyter nbconvert --to notebook --execute --allow-errors \
    --output-dir /ruta/temporal 05_combinar_datos.ipynb
  ```
  `jupyter execute --inplace` también existe, pero reescribe los outputs del cuaderno del usuario.
- Si se ejecutan varios cuadernos en paralelo, a veces un kernel muere por colisión de puertos ZMQ ("Address already in use"); reejecutar ese cuaderno solo.
- Tiempos aproximados: casi todos tardan segundos; 03 y 07 ~30–45 s; 09 ~3 min.

## Datos y carpetas generadas

- `data/`: CSVs de la muestra de Olist (pocos cientos de filas por tabla). `data_samples/` es una copia idéntica de respaldo para restaurar `data/`.
- **No reemplazar `data/` por el dataset completo**: las comprobaciones de los ejercicios tienen valores esperados fijos calculados sobre esta muestra (p. ej. `np.isclose(total, 27958.48)`).
- Al ejecutar los cuadernos se crean carpetas que no forman parte del curso y se pueden borrar:
  - `cache/` (módulo 01; y `cache/m09/`, ~9 GB, del módulo 09, que escala la muestra a ~1 M de filas sintéticas y escribe CSV/Parquet particionado).
  - `salida/` (módulo 10: Excel y Parquet del proyecto final).

## Estructura común de cada cuaderno

Todos los módulos siguen la misma plantilla, así que los cambios deben respetarla:

1. Celda markdown de portada: objetivos y tabla de secciones.
2. `## 0 · Setup`: imports, `DATA = Path("data")`, opciones de display y la función auxiliar `pendiente(*valores)` (devuelve `True` si algún valor sigue siendo `...`). Cada cuaderno es autocontenido: redefine el setup y vuelve a cargar los CSV que necesita con sus `dtype=`/`parse_dates=`/`usecols=`; no hay módulo Python compartido.
3. Secciones de teoría numeradas (`## 1 · …`).
4. `# ✍️ Ejercicios`: por cada ejercicio, enunciado en markdown (dificultad ⭐ a ⭐⭐⭐), una celda `# TU CÓDIGO AQUÍ` con variables asignadas a `...`, y una celda `# Comprobación` que con `if pendiente(...)` imprime "⏳ pendiente" y, si no, ejecuta `assert`s e imprime "✅ Ejercicio N correcto".
5. `# ✅ Soluciones` al final: reasignan las mismas variables del ejercicio. Por eso un cuaderno sin resolver se ejecuta completo sin errores (las comprobaciones solo informan "pendiente").

Al añadir o modificar un ejercicio, mantener la tripleta enunciado → `TU CÓDIGO AQUÍ` → comprobación con `pendiente`, y su solución correspondiente, comprobando que los valores esperados coinciden con la muestra de `data/`.

## Convenciones de código

- Indentación de **2 espacios** en todo el código de las celdas (bloques y sangrías colgantes); las continuaciones alineadas con el paréntesis de apertura se mantienen alineadas.
- Estilo pandas 3: dtype `str` por defecto, Copy-on-Write siempre activo (sin `.copy()` defensivos ni asignación encadenada), `merge` con `validate=`, *method chaining* con `assign(... lambda d: ...)`, `query` y `pipe`.
- Texto, comentarios y nombres de variables en español.
