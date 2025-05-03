# wseeds_cafe
# Procesamiento de datos de Finca de Café

Este proyecto automatiza el procesamiento de datos de una finca de café digitalizados a partir de tablas. El flujo de trabajo realiza las siguientes tareas:

1. **Descarga de datos:** Se descargan archivos CSV o Excel desde una carpeta pública de Google Drive que contiene los datos de la finca.
2. **Procesamiento inicial:** Se leen los archivos descargados, se extraen la finca y la actividad de los nombres de los archivos, se limpian los datos y se consolidan en DataFrames por actividad.
3. **Limpieza y transformación:** Se aplican funciones para limpiar y formatear las columnas de fecha y valor, eliminando caracteres innecesarios y corrigiendo formatos.
4. **Clustering de lotes:** Se utilizan algoritmos de clustering (Levenshtein distance con DBSCAN y Cosine Similarity con k-means) para agrupar lotes similares por finca.
5. **Guardado de resultados:** Los DataFrames procesados y con la información de clustering se guardan en un nuevo archivo Excel en Google Drive.

## Flujo de trabajo

1. **Acceso a Google Drive:** Se autentica el usuario y se monta Google Drive para acceder a los datos.
2. **Descarga de archivos:** Se descarga la carpeta pública de Google Drive que contiene la data de la finca.
3. **Procesamiento de los datos:** Se lee la data por archivos y se organiza y valida por actividad.
4. **Consolidación de los datos:** Se organiza la data en dataframes y se guarda en un archivo en drive.
5. **Limpieza y transformación de los datos:** Se formatea los datos para convertirlos a enteros y valores de fecha.
6. **Clustering de lotes:** Se utiliza k-means para agrupar los lotes.
7. **Guardado de los dataframes limpios y transformados:** Se guarda un nuevo archivo en drive con la información procesada.

## Funciones

### `process_file(filepath, filename)`

* **Objetivo:** Procesa un archivo individual (CSV o Excel), extrae información relevante y la agrega a un diccionario de tablas finales.
* **Entrada:**
    * `filepath`: Ruta al archivo.
    * `filename`: Nombre del archivo.
* **Salida:** No retorna ningún valor, actualiza el diccionario global `final_tables`.
* **Descripción:** Lee el archivo, extrae la finca y la actividad del nombre, limpia los datos, selecciona las columnas relevantes, asigna encabezados y agrega la tabla al diccionario `final_tables`.

### `clean_fecha_column(x)`

* **Objetivo:** Limpia y formatea valores en una columna de fecha.
* **Entrada:** `x`: Valor de la celda.
* **Salida:** Valor formateado como fecha o cadena original con "_formato_erroneo" si hay errores.
* **Descripción:** Intenta convertir el valor a una fecha con formato "dia-mes-2024". Si no es posible, devuelve el valor original con un indicador de error.

### `clean_valor_column(x)`

* **Objetivo:** Limpia y formatea valores en una columna de valor numérico.
* **Entrada:** `x`: Valor de la celda.
* **Salida:** Valor formateado como entero o cadena original con "_formato_erroneo" si hay errores.
* **Descripción:** Elimina puntos, espacios y guiones, e intenta convertir el valor a un entero. Si no es posible, devuelve el valor original con un indicador de error.

### `limpiar_y_transformar_dataframe(df)`

* **Objetivo:** Aplica las funciones de limpieza a un DataFrame completo.
* **Entrada:** `df`: DataFrame a limpiar.
* **Salida:** DataFrame limpio y transformado.
* **Descripción:** Elimina filas vacías, formatea columnas de fecha y valor utilizando las funciones `clean_fecha_column` y `clean_valor_column`, y elimina tildes de los nombres de las columnas.

### `cluster_levenshtein_dbscan(df, col_name='lote')`

* **Objetivo:** Agrupa valores en la columna especificada usando Levenshtein distance y DBSCAN.
* **Entrada:**
    * `df`: DataFrame.
    * `col_name`: Nombre de la columna para aplicar el clustering (por defecto 'lote').
* **Salida:** DataFrame con una nueva columna 'cluster_levenshtein' que indica la etiqueta del cluster.
* **Descripción:** Calcula la distancia de Levenshtein entre los valores de la columna, aplica DBSCAN para agrupar valores similares y agrega la etiqueta del cluster al DataFrame.

### `cluster_cosine_kmeans(df, col_name='lote', n_clusters=5)`

* **Objetivo:** Agrupa valores en la columna especificada usando Cosine Similarity y k-means.
* **Entrada:**
    * `df`: DataFrame.
    * `col_name`: Nombre de la columna para aplicar el clustering (por defecto 'lote').
    * `n_clusters`: Número de clusters para k-means (por defecto 5).
* **Salida:** DataFrame con una nueva columna 'cluster_cosine' que indica la etiqueta del cluster.
* **Descripción:** Calcula la similitud del coseno entre los valores de la columna, aplica k-means para agrupar valores similares y agrega la etiqueta del cluster al DataFrame.