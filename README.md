# 🚢 Titanic Survival Prediction MLOps

[![Python](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![Airflow](https://img.shields.io/badge/Airflow-Orchestration-blue.svg)](https://airflow.apache.org/)
[![GCP](https://img.shields.io/badge/GCP-Storage-red.svg)](https://cloud.google.com/)
[![Redis](https://img.shields.io/badge/Redis-Feature%20Store-red.svg)](https://redis.io/)
[![Flask](https://img.shields.io/badge/Flask-Serving-black.svg)](https://flask.palletsprojects.com/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-blue.svg)](https://www.docker.com/)
[![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-orange.svg)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Grafana-Visualization-orange.svg)](https://grafana.com/)

Implementación de una arquitectura MLOps end-to-end para el dataset del Titanic. El foco del proyecto es la construcción de un pipeline robusto que simula un entorno productivo real con orquestación, feature store y monitoreo de drift.

## Arquitectura

<img width="1225" height="590" alt="image" src="https://github.com/user-attachments/assets/d0217b18-0ace-4994-8a99-f6198d138ae6" />

## El Pipeline de Datos

1.  **Orquestación (Airflow):** Un DAG gestiona el ciclo de vida del dato. Extrae los raw data desde **Google Cloud Storage**, los procesa y los centraliza en una base de datos PostgreSQL.
2.  **Feature Engineering:** Se generan features sintéticas (como extracción de títulos y tamaño familiar) y se aplica SMOTE para balanceo de clases.
3. **Redis como Feature Store:** En lugar de leer CSVs en tiempo de inferencia, las features procesadas se almacenan en Redis para garantizar baja latencia en el serving.
4.  **Modelado:** Entrenamiento de un Random Forest optimizado con `RandomizedSearchCV`. El modelo final se serializa como artefacto listo para despliegue.

## Serving y Observabilidad

El modelo se expone mediante una API en **Flask** containerizada, integrando capas de seguridad y monitoreo:

*   **Detección de Drift (Alibi Detect):** Cada request entrante se analiza en tiempo real. Si la distribución de los datos del usuario difiere significativamente del set de entrenamiento (Data Drift), el sistema levanta una alerta antes de que el modelo degrade su performance.
*   **Stack de Monitoreo (Prometheus + Grafana):**
    *   **Prometheus** hace scraping de métricas customizadas expuestas por la API (conteo de predicciones, latencia, alertas de drift).
    *   **Grafana** visualiza la salud del sistema en dashboards en tiempo real.
