# Propuesta (Entregable 1)

**Entrega:** Viernes 21 de noviembre de 2025, 11:59 p.m.

**Equipo:** Sebastian Bolivar -- David Rojo 

**Título tentativo:** *Predicción de Churn con Modelos Secuenciales de Engagement en KKBOX (WSDM Cup 2018)*

---

## 1) Problema, motivación y alcance

### Problema
Predecir si un usuario **hará churn** (cancelará/no renovará) **después de expirar su suscripción**, usando su historial de comportamiento y transacciones del challenge **WSDM Cup 2018 (KKBOX)**.

### Motivación
- El churn suele estar precedido por **degradación del engagement** (menos actividad, menor tiempo total, cambios en patrones de escucha), lo cual es inherentemente **temporal/secuencial**.
- Modelos con agregados (promedios/último valor) pueden perder señales como caídas abruptas o tendencias; modelos secuenciales (GRU/Transformer) pueden capturar mejor estas dinámicas.

### Alcance
1. Construir un pipeline para formar, por usuario, una **secuencia diaria** (T×F) a partir de `user_logs_v2` y señales de `transactions_v2`.
2. Entrenar y comparar:
   - Baseline no secuencial (features agregadas + modelo clásico),
   - Baseline secuencial (GRU/LSTM),
   - **Transformer Encoder** (modelo principal).
3. Evaluar con métricas robustas al desbalance y realizar **mínimo 4 ablaciones**.
4. Análisis de errores e interpretabilidad básica (ej.: timesteps con mayor contribución).

---

## 2) Datos

### Fuente
Dataset público del challenge en Kaggle (WSDM – KKBOX Churn Prediction): `train.csv`, `members_v3.csv`, `transactions_v2.csv`, `user_logs_v2.csv`. :contentReference[oaicite:1]{index=1}

### Construcción de ejemplos
- Para cada usuario, generar una ventana de longitud **T** (p.ej., 30 días) con features diarias **F** (p.ej., `total_secs`, `num_25`, `num_100`, `num_unq`, ratios como *skip_rate*/*completion_rate*, etc.).
- Días sin actividad: cero + **máscara** (feature `is_missing_day` o mask explícita del modelo).

### Partición y control de fuga (leakage)
- Separar train/valid evitando fuga temporal (por ventanas de tiempo) o por usuarios (sin traslape).
- Usar únicamente información disponible **antes** del corte de predicción.

---

## 3) Plan de trabajo (resumen)

- **Semana 1:** EDA + pipeline (secuencias) + baseline con agregados (LogReg/XGBoost).
- **Semana 2:** Baseline secuencial (GRU) + tuning básico + evaluación PR-AUC.
- **Semana 3:** Transformer Encoder + tuning + primeras visualizaciones.
- **Semana 4:** Ablaciones + análisis de errores + interpretabilidad + redacción final.

---

## 4) Métricas
Por desbalance, priorizar:
- **PR-AUC (principal)**,
- ROC-AUC (secundaria),
- F1 para clase churn y/o Recall@k (si el escenario prioriza capturar churn).

---

## 5) Riesgos y mitigación
- **Dataset pesado (`user_logs_v2`)**: prototipar con muestreo, luego correr full; procesamiento por chunks.
- **Leakage temporal**: splits por tiempo/ventana y validación estricta.
- **Desbalance**: class weights o focal loss; selección de umbral basada en costos.

---

## 6) Breve estado del arte

1. **Descripción oficial del WSDM Cup 2018 (KKBOX)**: define tareas (recomendación + churn) y el contexto del desafío.  
   [https://wsdm-cup-2018.kkbox.events/](https://wsdm-cup-2018.kkbox.events/)   

2. **Artículo en ACM Digital Library del WSDM Cup 2018**: overview académico del reto y su framing.  
   “*WSDM Cup 2018: Music Recommendation and Churn Prediction*”.  
   https://dl.acm.org/doi/10.1145/3159652.3160605
   
---

