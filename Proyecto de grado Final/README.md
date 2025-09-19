# 📘 Tesis – Mortalidad Cardiovascular y Temperaturas Extremas  
**Autor:** Sofía Geraldine Vaca Cáceres  
**Universidad El Bosque – Ingeniería Ambiental**  

Este proyecto analiza la relación entre **temperaturas extremas** (máximas y mínimas) y la **mortalidad por enfermedades cardiovasculares (ECV)** en cuatro ciudades colombianas: **Bogotá, Medellín, Cali y Barranquilla**.  
Se aplican modelos estadísticos de tipo **GLM Poisson con splines** para capturar patrones temporales no lineales y se simulan escenarios climáticos futuros.  

---

## 🎯 Objetivos
1. Analizar la relación entre temperaturas extremas y mortalidad cardiovascular.  
2. Simular la evolución de la mortalidad bajo escenarios climáticos futuros (+1 °C, +2 °C, extremos).  
3. Evaluar la capacidad predictiva del modelo mediante validación temporal (80 % train, 20 % test).  

---

## ⚙️ Estructura del Notebook

### 1. Setup
- Instalación e importación de librerías: `numpy`, `pandas`, `statsmodels`, `patsy`, `matplotlib`, `scipy`.  
- Configuración de rutas para los datasets:  
  - `temperaturas.csv`  
  - `humedad_relativa.csv`  
  - `Mortalidad.csv`  

### 2. Normalización de columnas
- Limpieza y estandarización de nombres (`tmax`, `tmin`, `tmean`, `humedad`, `muertes`, `ciudad`).  
- Creación de la columna `fecha` a partir de `anio + semana`.  

### 3. Unificación de datasets
- Unión (`merge`) de los tres CSV en un único dataframe `dfw`.  
- Imputación de valores faltantes de humedad mediante la mediana por ciudad.  

### 4. Construcción de fórmulas seguras
- Función `build_formula_for_df`: arma la fórmula solo con las variables presentes.  
- Variables de control: `t_ordinal`, `year_sin`, `year_cos`, `humedad`.  

### 5. Ajuste de modelos por ciudad
- Función `fit_city`: ajusta modelos GLM Poisson.  
- Reporta: AIC, deviance, número de observaciones y variables utilizadas.  

### 6. Curvas de Riesgo Relativo (RR) + MMT
- Función `rr_curve`: calcula curvas de riesgo relativo para `tmax`, `tmin` o `tmean`.  
- Estimación de la **Temperatura Mínima de Mortalidad (MMT)**.  

### 7. Escenarios Climáticos
- Escenarios analizados:  
  - **Base**  
  - **+1 °C**  
  - **+2 °C**  
  - **Extremos (p95/p05)**  
- Estimación de muertes previstas bajo cada escenario.  

### 8. Evaluación Predictiva (split 80/20)
- **8.0 Diagnóstico:** columnas requeridas por el modelo.  
- **8.1 Split temporal:** división 80 % entrenamiento / 20 % prueba.  
- **8.2 Entrenamiento seguro:** entrenamiento del modelo en TRAIN.  
- **8.3 Diseño con fallback:** generación de columnas faltantes si el diseño lo exige.  
- **8.4 Métricas:** cálculo de MAE, RMSE, Deviance y R².  
- **8.5 Gráficas:**  
  - Serie Observado vs Predicho  
  - Scatter Predicho vs Observado  
  - Residuos en Test  
- **8.6 Intervalos de Confianza (IC):** bandas de la media predicha.  

---

## 📊 Resultados

### 1. Diagnóstico de diseño
Todas las ciudades utilizaron `tmax` y `tmin` junto con controles de humedad y estacionalidad:  

```
[Barranquilla] diseño requiere: ['humedad','t_ordinal','tmax','tmin','year_cos','year_sin']; rr_vars=['tmax','tmin']
[Bogotá]       diseño requiere: ['humedad','t_ordinal','tmax','tmin','year_cos','year_sin']; rr_vars=['tmax','tmin']
[Cali]         diseño requiere: ['humedad','t_ordinal','tmax','tmin','year_cos','year_sin']; rr_vars=['tmax','tmin']
[Medellín]     diseño requiere: ['humedad','t_ordinal','tmax','tmin','year_cos','year_sin']; rr_vars=['tmax','tmin']
```

### 2. Métricas predictivas (test set)

| Ciudad       | n_train | n_test | Test MAE | Test RMSE | Test Deviance | Test R² |
|--------------|---------|--------|----------|-----------|---------------|---------|
| Barranquilla | 424     | 106    | 5.23     | 6.97      | 196.05        | 0.11    |
| Bogotá       | 424     | 106    | 25.39    | 30.76     | 1042.81       | 0.01    |
| Cali         | 424     | 106    | 11.93    | 14.28     | 636.68        | 0.00    |
| Medellín     | 424     | 106    | 13.94    | 17.38     | 581.55        | 0.04    |

➡️ El poder predictivo en test es **bajo**: el modelo captura tendencias promedio pero no reproduce picos extremos semanales.  

---

## 🖼️ Ejemplos de Figuras

- Serie Observado vs Predicho – Bogotá  
- Scatter Predicho vs Observado – Cali  
- Residuos en Test – Barranquilla  

Ejemplo en LaTeX:

```latex
egin{figure}[H]
    \centering
    \includegraphics[width=0.45	extwidth]{figs/Backtest_Serie_Bogotá.png}
    \caption{Serie Observado vs Predicho de mortalidad cardiovascular en Bogotá (2010–2019).}
    \label{fig:serie_bogota}
\end{figure}
```

---

## ✅ Conclusiones

1. Los modelos **GLM Poisson con splines** permiten capturar la **forma no lineal** de la relación entre temperaturas extremas y mortalidad cardiovascular.  
2. Se identifica una **temperatura mínima de mortalidad (MMT)** distinta en cada ciudad, lo que confirma la heterogeneidad climática y de vulnerabilidad poblacional.  
3. Los escenarios simulados (+1 °C, +2 °C y extremos) muestran un **aumento potencial en muertes por ECV**, especialmente en ciudades cálidas como Barranquilla.  
4. El desempeño predictivo (R² < 0.15 en test) indica que el modelo reproduce tendencias globales pero no los picos semanales. Se sugiere explorar modelos más avanzados:  
   - **DLNM (Distributed Lag Non-Linear Models)** para incorporar rezagos de temperatura.  
   - **Modelos de sobredispersión (NegBin)** para reducir el sesgo en conteos.  
   - **Estructuras GEE/GAM** para capturar autocorrelaciones temporales.  
5. Este análisis respalda la importancia de **adaptación al cambio climático** en salud pública, dado que temperaturas extremas impactan directamente la mortalidad cardiovascular.  

---

## 🚀 Instrucciones de Uso

### Ejecución en Google Colab
1. Abre [Google Colab](https://colab.research.google.com/).  
2. Sube el notebook `Tesis_ECV_Temperaturas_ModeloPredictivo_ULTRA_ROBUSTO_v2.ipynb`.  
3. Sube también los archivos de datos:  
   - `temperaturas.csv`  
   - `humedad_relativa.csv`  
   - `Mortalidad.csv`  
4. Ejecuta todas las celdas en orden (Ctrl+F9 o `Entorno de ejecución > Ejecutar todo`).  
5. Los resultados se guardan en:  
   - Tablas: `stats_df.csv`, `evaluacion_train_test_por_ciudad.csv`  
   - Gráficas: carpeta `/figs/`  

### Requerimientos
- Python ≥ 3.9  
- Librerías: `numpy`, `pandas`, `matplotlib`, `statsmodels`, `patsy`, `scipy`  

Instalación rápida (si se ejecuta en local):
```bash
pip install numpy pandas matplotlib statsmodels patsy scipy
```

---

## 📌 Notas Finales
- Este notebook está diseñado para ser **reproducible en Colab** sin dependencias externas adicionales.  
- La carpeta `figs/` contendrá todas las gráficas en **alta resolución** listas para incluir en informes, presentaciones o en la tesis.  
