# DashView

Dashboard de analítica de experiencia de cliente: sentimiento, quejas y riesgo de deserción (churn), construido con Python, Dash y SQLite.

## Sobre este repositorio

Este proyecto es una copia independiente de un trabajo grupal desarrollado originalmente en [JoacoLucen/DashView](https://github.com/JoacoLucen/DashView), junto con otros colaboradores que también aparecen en el historial de commits. Como todo el desarrollo del grupo se subió directamente a ese repositorio compartido, este no es un fork técnico sino una copia completa de ese historial, creada para tener una versión propia y estable donde mostrar mi contribución puntual al proyecto.

### Mi contribución

Según los commits que figuran bajo mi autoría en el historial:

- Implementación completa del sistema de login/logout por stakeholder, con sesión persistida en el navegador y acceso restringido a la pestaña correspondiente a cada usuario (`app.py`).
- Filtros de años más detallados y separados por stakeholder, corrección de un bug que no mostraba si había algún dataset cargado, y mejoras en el pipeline de ETL.
- Rediseño de UX y métricas: ajustes de lenguaje, cambios de diseño y revisión de tipos de datos usados en las métricas.
- Limpieza de `node_modules` del control de versiones.

## Qué hace el dashboard

Analiza señales de clientes (reseñas de tiendas de apps, quejas regulatorias tipo CFPB, etc.) para medir sentimiento, deserción y riesgo, organizadas en 4 vistas según el stakeholder que inicia sesión:

- **Marketing**: señales totales, proxy de NPS, % promotores/detractores, tendencia trimestral de quejas/deserción, sentimiento por canal.
- **Dirección General**: señales de deserción, exposición regulatoria (% de quejas CFPB), benchmark competitivo (empresas con menor satisfacción), heatmap de satisfacción por empresa y producto.
- **Retención y Facturación**: tasa de escalada de quejas (de reclamo a queja formal), tiempo promedio hasta la deserción, radar de riesgo por producto, quejas agrupadas por tema (facturación, atención al cliente, reporte crediticio, cuenta, préstamos, acceso a la app) y por geografía.
- **Equipo de Producto**: comparación AppStore vs. Google Play, distribución de calificaciones, agrupación de problemas reportados de la app (caídas, lentitud, bugs, login), evolución interanual de volumen y sentimiento.

El acceso a cada pestaña está restringido por usuario: cada stakeholder ve únicamente la suya (ver `CREDENCIALES.md`).

## Stack

- **Python**
- **Dash** + **dash-bootstrap-components** (interfaz)
- **Plotly** (gráficos)
- **Polars** (procesamiento de datos)
- **SQLite** (persistencia, vía el módulo estándar `sqlite3`)

## Cómo correrlo

1. Instalar dependencias:
   ```bash
   pip install -r requirements.txt
   ```
2. Ejecutar la app:
   ```bash
   python app.py
   ```
3. Abrir `http://localhost:8050` en el navegador.
4. Iniciar sesión con alguno de los usuarios de `CREDENCIALES.md` (son credenciales de demo, necesarias para poder comparar las distintas vistas por stakeholder).

No hay `docker-compose.yml` ni configuración de despliegue en este repo: la app corre de forma standalone, con una base SQLite local, sin necesidad de levantar servicios externos.

**Nota:** el repositorio no incluye ningún dataset de ejemplo. La base SQLite se crea vacía; para ver contenido en las 4 vistas hay que cargar primero un dataset propio (`.csv`, `.parquet`, `.json`/`.jsonl`, `.db` o `.zip`) desde la interfaz, como se describe en la sección siguiente.

## Carga de datos

Desde la propia interfaz se pueden importar datasets en formato `.csv`, `.parquet`, `.json`/`.jsonl` o `.db` (sueltos o dentro de un `.zip`), y eliminarlos —individualmente o todos juntos— desde el modal de "Datasets". La carga y el borrado se procesan en segundo plano, con una barra de progreso que consulta el estado periódicamente.

## Estructura

- `app.py`: layout, callbacks, autenticación y renderizado de las 4 vistas.
- `src/database_manager.py`: conexión a SQLite y ejecución de consultas (con cacheo de resultados).
- `src/etl_pipeline.py`: lectura, limpieza y carga de los datasets a la base.
- `src/metrics.py`: cálculo de las métricas e indicadores usados en cada vista.
- `check_cols.py`, `check_coverage.py`, `check_data.py`, `check_years.py`, `test_metrics.py`: scripts sueltos de inspección/verificación de datos, no forman parte de una suite de tests formal.
