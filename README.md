# Telecom_Analysis

📶 Análisis de Clientes — ConnectaTel
Análisis exploratorio del comportamiento de los clientes de ConnectaTel, una empresa de telecomunicaciones en Latinoamérica, con datos registrados hasta 2024.

🎯 Objetivo del proyecto
Evaluar el comportamiento de los clientes para:

Limpiar y validar la calidad de los datos (nulos, sentinels, fechas imposibles).
Construir un perfil estadístico de uso por cliente (mensajes, llamadas y minutos).
Detectar comportamientos atípicos (outliers).
Crear segmentos de clientes por edad y por nivel de uso.
Traducir los hallazgos en recomendaciones de negocio: retención, mejora de planes y nuevas ofertas.


🗂️ Datasets utilizados
Archivo	Filas	Descripción	Columnas principales
plans.csv	2	Planes actuales (Básico y Premium)	plan_name, messages_included, gb_per_month, minutes_included, usd_monthly_pay, usd_per_gb, usd_per_message, usd_per_minute
users_latam.csv	4,000	Información de los clientes	user_id, first_name, last_name, age, city, reg_date, plan, churn_date
usage.csv	40,000	Detalle de uso: llamadas y mensajes	id, user_id, type (call/text), date, duration (min), length (caracteres)


🧩 Etapas del análisis
1. Carga y exploración Carga de los tres datasets y revisión de estructura con .head(), .shape e .info().

2. Identificación de problemas de calidad
      Conteo y proporción de valores nulos.
      Detección de sentinels (-999 en age, "?" en city).
      Conversión de fechas con pd.to_datetime(..., errors="coerce") y detección de años imposibles (2026).

3.Limpieza básica
      age: -999 reemplazado por la mediana (48).
      city: "?" reemplazado por pd.NA.
      reg_date: fechas posteriores a 2024 marcadas como NaT.
      duration / length: se confirmó que sus nulos son MAR (dependen de type) y se dejaron como nulos.

4. Resumen de uso por usuario Agregación de usage por user_id (cant_mensajes, cant_llamadas, cant_minutos_llamada) y unión con users en el DataFrame user_profile.

5. Distribuciones y outliers
      Histogramas por plan (hue='plan') para edad, mensajes, llamadas y minutos.
      Boxplots y cálculo de límites con el método IQR. Se decidió mantener los outliers, porque son valores plausibles de clientes de alto consumo.

6. Segmentación de clientes
      grupo_uso: Bajo uso / Uso medio / Alto uso (según llamadas y mensajes).
      grupo_edad: Joven (< 30) / Adulto (30–59) / Adulto Mayor (≥ 60).
      Visualización con countplot.
      Conclusiones y recomendaciones Hallazgos principales y propuestas para la oferta de planes.

▶️ Cómo ejecutar el notebook
Opción A — Google Colab (recomendada)
Abre Google Colab → Archivo › Subir cuaderno y selecciona el archivo .ipynb del proyecto.
En el panel izquierdo (📁 Archivos), sube plans.csv, users_latam.csv y usage.csv.
Cambia las rutas de carga del notebook, porque en Colab los archivos quedan en /content/:

plans = pd.read_csv('/content/plans.csv')
users = pd.read_csv('/content/users_latam.csv')
usage = pd.read_csv('/content/usage.csv')
Si prefieres tener los archivos en Google Drive:

from google.colab import drive
drive.mount('/content/drive')
plans = pd.read_csv('/content/drive/MyDrive/<carpeta>/plans.csv')
Ejecuta todo con Entorno de ejecución › Ejecutar todas (Ctrl + F9).

