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
Carga y exploración Carga de los tres datasets y revisión de estructura con .head(), .shape e .info().

Identificación de problemas de calidad

Conteo y proporción de valores nulos.
Detección de sentinels (-999 en age, "?" en city).
Conversión de fechas con pd.to_datetime(..., errors="coerce") y detección de años imposibles (2026).
Limpieza básica

age: -999 reemplazado por la mediana (48).
city: "?" reemplazado por pd.NA.
reg_date: fechas posteriores a 2024 marcadas como NaT.
duration / length: se confirmó que sus nulos son MAR (dependen de type) y se dejaron como nulos.
Resumen de uso por usuario Agregación de usage por user_id (cant_mensajes, cant_llamadas, cant_minutos_llamada) y unión con users en el DataFrame user_profile.

Distribuciones y outliers

Histogramas por plan (hue='plan') para edad, mensajes, llamadas y minutos.
Boxplots y cálculo de límites con el método IQR. Se decidió mantener los outliers, porque son valores plausibles de clientes de alto consumo.
Segmentación de clientes

grupo_uso: Bajo uso / Uso medio / Alto uso (según llamadas y mensajes).
grupo_edad: Joven (< 30) / Adulto (30–59) / Adulto Mayor (≥ 60).
Visualización con countplot.
Conclusiones y recomendaciones Hallazgos principales y propuestas para la oferta de planes.

💡 Hallazgos principales
Los planes están sobredimensionados: el usuario típico envía unos 5 mensajes, hace 4 llamadas y habla unos 20 minutos (medianas), muy por debajo de lo que incluye cualquiera de los dos planes.
Básico y Premium se comportan igual en cantidad de mensajes y llamadas. La diferencia está en los minutos: Premium tiene proporcionalmente más usuarios con llamadas largas.
La edad casi no influye en la elección de plan. Los adultos mayores muestran algo más de preferencia por Premium.
El 11.7% de los clientes ya canceló (churn_date).
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

Opción B — Local (Jupyter)
Instala Python 3.9 o superior.

Instala las dependencias:

pip install pandas seaborn matplotlib jupyter
Coloca los CSV en una carpeta datasets/ junto al notebook y ajusta las rutas a datasets/plans.csv, etc.

Abre el notebook:

jupyter notebook
🔁 Guía de reproducción
Ejecuta las celdas en orden, de arriba hacia abajo. Cada etapa depende de la anterior: la limpieza modifica users y usage, y la segmentación usa user_profile.
Reinicia el kernel antes de repetir el análisis (Entorno de ejecución › Reiniciar y ejecutar todo). Algunas celdas modifican los datos en su lugar, por ejemplo el reemplazo de -999 o de fechas por NaT. Si se ejecutan dos veces sin recargar los CSV, los resultados pueden cambiar.
Verifica los puntos de control para confirmar que la limpieza salió bien:
users['age'].min() → 18
(users['city'] == '?').sum() → 0
users['reg_date'].dt.year.value_counts(dropna=False) → 2022, 2023, 2024 y 40 NaN
user_profile.shape[0] → 4000
Las gráficas usan la paleta ['skyblue', 'green'] para Básico y Premium, así que deben verse iguales en cada ejecución.
🛠️ Herramientas
Python 3.9+
pandas: carga, limpieza y agregación de datos
seaborn y matplotlib: visualizaciones
