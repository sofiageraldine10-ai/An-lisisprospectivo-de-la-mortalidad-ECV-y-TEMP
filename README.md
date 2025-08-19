# Objetivo Específico 1 — Exploración de temperaturas (2010–2019)
**Ciudades:** Bogotá, Medellín, Cali, Barranquilla  
**Alcance:** Solo análisis descriptivo de **Tmax** y **Tmin** (SIN RR ni mortalidad). Base para el Objetivo 2.

## 🎯 Propósito
Caracterizar patrones temporales de temperatura (series, estacionalidad, extremos y anomalías) por ciudad, usando CSVs subidos (NASA POWER u otros), para preparar la integración con mortalidad en el siguiente objetivo.

## ▶️ Ejecución (Google Colab)
1. Subir archivos:
   ```python
   from google.colab import files
   uploaded = files.upload()
   ```
2. Ejecutar la **celda de lectura robusta (V2)** que detecta encabezado/fecha/columnas y construye `temps` con:
   ```
   date, city, tmax, tmin, year, month, doy, ym, heat_extreme, cold_extreme
   ```
3. Ejecutar las celdas de **gráficas** (series suavizadas, climatología mensual, extremos por año, distribuciones, mapas de anomalías).
4. (Opcional) Empaquetar resultados (ZIP) desde `RESULTS_DIR`.

## 📥 Entradas esperadas
CSVs con temperatura diaria por ciudad. Se admiten fechas en `YEAR/MO/DY`, `DATE/FECHA`, `YEAR+DOY` o `YYYYMMDD`. Alias soportados para temperaturas: `T2M_MAX/T2M_MIN`, `TMAX/TMIN`, `LST_DAY/LST_NIGHT`, etc.

## 🖼️ Salidas (carpeta `resultados_obj1_intro/`)
- `serie_diaria_suavizada_{Ciudad}.png`
- `climatologia_mensual_{Ciudad}.png`
- `extremos_por_anio_{Ciudad}.png`
- `distribuciones_{Ciudad}.png`
- `anomalias_tmax_{Ciudad}.png`, `anomalias_tmin_{Ciudad}.png`
- `exposicion_extremos_resumen.csv`

## 🗂️ Estructura sugerida
```
notebooks/01_exploracion_temperaturas.ipynb
resultados_obj1_intro/ (se crea al ejecutar)
README.md
```

## ⚠️ Notas rápidas
- Este cuaderno **no** estima RR ni ajusta modelos de mortalidad.
- Si aparece “No pude construir la fecha” o “No identifiqué Tmax/Tmin”, revisar los nombres de columnas impresos por el `[DEBUG]` y añadir el alias en la celda de lectura.
- Para el **Objetivo 2**, se integrará mortalidad y se construirán modelos/alertas.
