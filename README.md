# Preprocesamiento de Dataset — Detección de Lavado de Activos

**Semillero de Investigación — PerceptIA**  
**Equipo:** Nexus  
**Dataset:** `Datos_KIA.csv` — Registros de compra de vehículos (ASIA / KMOT)

---

## Descripción del Proyecto

Este proyecto corresponde a la etapa de **preprocesamiento de datos** dentro de un pipeline de Machine Learning orientado a la **detección de lavado de activos** en transacciones de compra de vehículos.

El dataset contiene **32.606 registros** y **55 columnas** con información demográfica, financiera, laboral y de vínculos con Personas Expuestas Políticamente (PEP) de clientes de las marcas ASIA y KMOT.

---

##  Estructura del Repositorio

```
nexus-kia-preprocessing/
│
├── preprocesamiento_lavado_activos_GRUPO_NEXUS.ipynb   # Notebook principal
├── README.md                                            # Este archivo
└── .gitignore                                           # Archivos a ignorar
```

> ⚠️El archivo `Datos_KIA.csv` **no se incluye** en el repositorio por contener datos sensibles de clientes. Para ejecutar el notebook, debes cargarlo manualmente en Google Colab.

---

##  Qué se hizo — Pasos del Preprocesamiento

| # | Paso | Descripción |
|---|---|---|
| 1 | **Importación de librerías** | `pandas`, `numpy`, `matplotlib`, `seaborn`, `sklearn` |
| 2 | **Carga e inspección inicial** | Dimensiones, tipos de datos, estadísticas descriptivas, duplicados |
| 3 | **Análisis de valores nulos** | Cuantificación, visualización y clasificación por estrategia de tratamiento |
| 4 | **Eliminación de columnas irrelevantes** | 11 columnas eliminadas: vacías, redundantes, varianza cero e identificadores personales |
| 5 | **Tratamiento de valores nulos** | Imputación diferenciada: mediana, `'SIN_DATO'`, `'SIN_COINCIDENCIA'` (excepción de negocio) |
| 6 | **Conversión de tipos de datos** | Fechas a `datetime`, `Edad` a `int`, variable derivada `Trimestre` |
| 7 | **Limpieza de texto** | Normalización strip + upper, estandarización de variantes (`SN`) |
| 8 | **Tratamiento de outliers** | Winsorización al percentil 99; creación de variable `alerta_ingreso_gasto` |
| 9 | **Codificación categórica** | Label Encoding en 12 variables (Forma_Pago, Vinculo_PEP, coincidencias, etc.) |
| 10 | **Normalización numérica** | MinMaxScaler en 6 variables continuas → rango [0, 1] |
| 11 | **Validación y exportación** | 0 nulos, 0 duplicados, exportación a `Datos_KIA_preprocesado.csv` |

---

##  Decisiones Metodológicas Clave

### Excepción de regla de negocio — columnas de coincidencias con listas de riesgo
Las variables `coincidencias_listas`, `coincidencias_listas_Conyuge` y `coincidencias_listas_Tercero` superan el 97–99% de nulos, pero **no se eliminan**. En detección de lavado de activos, la ausencia de valor en estas columnas indica que el cliente no aparece en ninguna lista de riesgo, lo cual es en sí mismo una señal predictiva. Se imputan con `'SIN_COINCIDENCIA'` para crear una categoría explícita que el modelo pueda aprender a distinguir.

### Eliminación de columnas de nombres de personas
Los nombres completos de clientes, cónyuges y terceros funcionan como identificadores únicos y no aportan valor predictivo generalizable. Conservarlos podría provocar *data leakage* (el modelo memorizaría combinaciones nombre-etiqueta del entrenamiento). Se eliminaron las 6 columnas (versiones originales y limpias).

### Winsorización en lugar de eliminación de outliers
Dado que valores extremos en variables financieras pueden ser precisamente señales de alerta de lavado de activos, se optó por winsorizarlos al percentil 99 en lugar de eliminar los registros, preservando así la señal de anomalía.

---
## Resultado del Preprocesamiento

| Métrica | Valor |
|---|---|
| Filas | 32.606 |
| Columnas finales | 58 (44 originales + 12 codificadas + 2 derivadas) |
| Valores nulos | 0 |
| Duplicados | 0 |
| Columnas eliminadas | 11 |
| Variables de alerta generadas | `alerta_ingreso_gasto` |

---

## Tecnologías utilizadas

- Python 3.10+
- pandas
- numpy
- matplotlib / seaborn
- scikit-learn (`LabelEncoder`, `MinMaxScaler`)
- Google Colab

---

## Cómo ejecutar el notebook

1. Abre [Google Colab](https://colab.research.google.com/)
2. Sube el archivo `preprocesamiento_lavado_activos_GRUPO_NEXUS.ipynb`
3. Carga el archivo `Datos_KIA.csv` con:
   ```python
   from google.colab import files
   files.upload()
   ```
4. Ejecuta todas las celdas en orden (`Runtime > Run all`)

---

## 👥 Equipo

**Semillero PerceptIA — Equipo Nexus**
