# tfg-aurora-data

Código Python para el procesamiento, limpieza y categorización de datos de AURORA Energy Tracker (JSON a CSV) para su posterior análisis en SPSS.

---

## Descripción del Proyecto

Este repositorio contiene el flujo de trabajo (*pipeline*) desarrollado en Python para el procesamiento, depuración y estructuración de los datos procedentes de la aplicación **AURORA Energy Tracker**. El objetivo principal es transformar un conjunto de datos masivo e inicialmente anidado en ficheros relacionales estructurados, optimizados para su explotación estadística directa en **SPSS**.

El desarrollo se ha estructurado de forma modular en dos cuadernos de Jupyter (`.ipynb`) secuenciales para garantizar la reproducibilidad y el orden metodológico del proceso.

---

## Estructura del Pipeline de Datos

### 1. `01_JSON_a_CSV.ipynb` (Extracción y Aplanado)
Este cuaderno realiza la ingesta de los datos en bruto y resuelve la complejidad de las estructuras jerárquicas del JSON.
* **Carga de Datos:** Importación del archivo maestro `user_data.json` (asegurando la integridad mediante la asignación por defecto de identificadores de usuario ausentes).
* **Aplanado (*Flattening*):** Desanidación de los registros de la lista de consumos mediante iteración controlada y el uso de `pd.json_normalize()`. Esto unifica los metadatos comunes del usuario con cada evento de consumo individual.
* **Exportación Inicial:** Almacenamiento del dataset bruto unificado en formatos estándar (`consumptions.csv` y `consumptions.xlsx`), generando una matriz inicial de 119.124 filas y 43 columnas.

### 2. `02_limpieza_y_segmentacion.ipynb` (Depuración y Selección de Atributos)
Este cuaderno implementa las reglas de negocio y calidad de datos para eliminar anomalías (*outliers*) y segmentar el dataset por tipología de consumo energético.
* **Selección de Variables:** Filtrado de los 18 atributos más relevantes para los objetivos de la investigación.
* **Tratamiento Demográfico:** Identificación y asignación de nulos (`NaN`) para años de nacimiento inconsistentes (registros fuera del rango 1950-2007) y generación de una nueva columna categórica para cohortes generacionales (*BabyBoom, GenX, GenY, GenZ, Alfa*).
* **Limpieza de Consumos Incongruentes:**
  * *Electricidad:* Exclusión de consumos fuera de la ventana técnica de [50, 5000) kWh.
  * *Transporte:* Eliminación de consumos negativos o superiores a 12,432 kWh, preservando los registros en 0 únicamente si corresponden a movilidad activa (`walking` o `bike`).
  * *Calefacción:* Eliminación de consumos atípicos inferiores a 50 kWh.
* **Optimización Estructural y Consolidación:** Actualización del dataset maestro mediante `.update()` y eliminación de columnas redundantes o vacías para cada caso específico (p. ej., eliminar variables de transporte y calefacción dentro de la tabla de electricidad), previniendo la dispersión de datos (*sparsity*).

---

## Outputs Generados

El pipeline concluye de manera automática con la exportación de cuatro productos de datos listos para ser importados en SPSS:

1. **`clean_dataset.csv`**: Matriz global consolidada y depurada (119.124 filas × 19 columnas).
2. **`electricity.csv`**: Subconjunto optimizado para consumos de electricidad (964 filas × 14 columnas).
3. **`transport.csv`**: Subconjunto optimizado para consumos de movilidad (117.712 filas × 15 columnas).
4. **`heating.csv`**: Subconjunto optimizado para consumos de climatización (448 filas × 14 columnas).

---

## Requisitos e Instalación

Para ejecutar estos cuadernos de manera local, asegúrate de contar con Python 3.x instalado junto con las siguientes librerías de soporte:

```bash
pip install pandas numpy openpyxl ipykernel
