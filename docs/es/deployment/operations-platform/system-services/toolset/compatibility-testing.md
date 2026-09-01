# Pruebas de Compatibilidad

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Resumen de la página

La página de prueba de compatibilidad se utiliza para verificar la configuración del almacenamiento de objetos, conectividad, compatibilidad de carga y rendimiento de carga. La página se divide en:

1. Configuración de almacenamiento;
2. Prueba de compatibilidad;
3. Prueba de rendimiento.

## 2. Configuración de almacenamiento 

### 2.1 Descripción del elemento de configuración 

| Elemento de configuración | Función |
| --- | --- |
| Clave de acceso | Identificador de identidad de acceso del almacenamiento de objetos, es decir, AK |
| Clave secreta | La clave de acceso que viene con AK, es decir, SK |
| Punto de Extremidad | Dirección del servicio de almacenamiento de objetos |
| Nombre del Bucket | Depósito objetivo a ser detectado |
| Región | Área de ubicación del depósito de almacenamiento |
| Punto de acceso público | El dominio público que usa el navegador para acceder al almacenamiento de objetos, opcional |
| Estilo de ruta | Usando el formato 'endpoint/bucket/object' para acceder a objetos, servicios como MinIO generalmente necesitan habilitar |

### 2.2 Complete la configuración 
1. Haga clic en "Rellenar depósito de adjuntos" o "Rellenar depósito de contenidos" según sea necesario. 
2. El sistema rellena automáticamente configuraciones como AK, SK, Endpoint, Bucket, Región, etc., correspondientes al entorno actual. 
3. Si no usa el llenado automático, también puede completar manualmente todas las configuraciones. 
4. Verifique si el endpoint contiene el protocolo y puerto correctos. 
5. Verifique si el Nombre del Bucket es el bucket que necesita detectarse esta vez. 
6. Verifique si la Región coincide con la región real del almacenamiento de objetos.
7. Si necesita que el navegador acceda directamente al almacenamiento de objetos, complete el Punto de Acceso Público.
8. Decida si habilitar el Estilo de Ruta dependiendo del tipo de almacenamiento de objetos.

Después de completar automáticamente el bucket de Adjuntos esta vez:

- Bucket: `shimo-attachments`;
- Punto de Acceso: completado automáticamente por el sistema;
- Región: `cn-north-1`;
- Punto de Acceso Público: completado automáticamente por el sistema;
- Estilo de Ruta: habilitado.

Después de hacer clic en "Rellenar contenido del bucket," el Bucket puede cambiar automáticamente a `file-contents`.

> La Clave Secreta es información sensible; no la muestre en texto plano en capturas de pantalla, chats o tickets.

## 3. Prueba de Compatibilidad

La prueba de compatibilidad verificará secuencialmente la carga desde backend, la carga directa desde navegador y la carga multipart.

### 3.1 Comenzar la Prueba

1. Complete la configuración de almacenamiento.
2. Haga clic en la pestaña "Prueba de Compatibilidad".
3. Confirme nuevamente que la configuración de Bucket, Punto de Acceso, Región, Punto de Acceso Público y Estilo de Ruta sea correcta.
4. Haga clic en "Iniciar Prueba de Compatibilidad."
5. Espere a que la página indique "Prueba de Compatibilidad Completada."
6. Verifique si el estado resumido en la página es "Todos Aprobados."
7. Verifique el estado, la duración y la información de resultados de las tres pruebas por separado.

### 3.2 Prueba de Carga desde Backend

Esta prueba se utiliza para verificar la conectividad de red y los permisos de escritura desde el servicio backend al almacenamiento de objetos.

1. El backend genera un archivo de texto de prueba.
2. El backend escribe el archivo en el Bucket de destino.
3. La página muestra la duración de la carga y la ruta del objeto de prueba.
4. Un estado de éxito verde indica que la red del backend y los permisos de escritura son normales.

Resultado actual: Carga exitosa, duración `157 ms`.

### 3.3 Prueba de Carga Directa desde el Navegador

Esta prueba se utiliza para verificar el enlace para que el navegador cargue directamente al almacenamiento de objetos a través de PostPolicy.

1. La página solicita al backend el PostPolicy requerido para la carga directa.
2. El navegador utiliza el Endpoint Público para cargar archivos directamente al almacenamiento de objetos.
3. La página verifica el HTTP código de estado devuelto por el almacenamiento de objetos.
4. HTTP 204 indica que la carga directa desde el navegador fue exitosa.

Resultado esta vez: Carga directa desde el navegador exitosa, tomó `61 ms`, código de estado `204`.

### 3.4 Prueba de Carga Multipartida

Esta prueba se utiliza para verificar el proceso completo de carga multipartida de archivos grandes.

1. Inicializar la tarea de carga multipartida.
2. Dividir el archivo de prueba en varias partes.
3. Cargar cada parte secuencialmente.
4. Llamar a la interfaz de fusión para completar la creación del objeto.
5. La página muestra `multipart upload succeeded`, indicando que todo el proceso fue exitoso.

Resultado esta vez: Carga multipartida exitosa, tomó `1746 ms`.

### 3.5 Descripción de Objetos de Prueba

La prueba de compatibilidad realizará escrituras reales en el Bucket de destino, y las rutas de los objetos de prueba del backend suelen ser similares a:

```text
compatibility-tests/<RANDOM UUID>.txt
```

1. Confirme que el Bucket de destino es correcto antes de ejecutar las pruebas.
2. No ejecute pruebas indiscriminadamente en el Bucket de producción incorrecto.
3. Después de la prueba, puede revisar y limpiar los objetos de prueba según la política de limpieza del entorno.

## 4. Pruebas de Rendimiento

Las pruebas de rendimiento se utilizan para medir el tiempo de carga y el rendimiento para diferentes tamaños de archivo.

### 4.1 Configuración de Pruebas de Rendimiento

1. Haga clic en la pestaña "Pruebas de Rendimiento".
2. Seleccione el modo de prueba; la página predetermina a "Navegador Directo".
3. Seleccione el Tipo de Contenido; puede usar `application/octet-stream` por defecto.
4. Elija el tamaño de archivo que desea probar.
5. La página soporta 1, 5, 8, 10, 12, 15, 20 y 25 MB.
6. En el entorno de prueba, puede seleccionar primero 1 MB para una validación rápida.
7. Para una comparación formal de rendimiento, seleccione varios tamaños de archivo para la prueba.

### 4.2 Iniciar Prueba de Rendimiento

1. Confirme que la configuración de almacenamiento es correcta.
2. Confirme que se ha seleccionado al menos un tamaño de archivo.
3. Haga clic en 'Iniciar Prueba de Rendimiento'.
4. Espere a que todos los archivos terminen de cargarse.
5. Revise el rendimiento promedio, el tiempo más corto y el tiempo más largo.
6. Revise el estado, el tiempo transcurrido, el rendimiento y los mensajes de error para cada tamaño de archivo.
7. Si ocurren fallos, primero revise la red del navegador, el Punto Final Público, la configuración de origen cruzado y el estado del almacenamiento de objetos.

### 4.3 Resultados de Esta Prueba

Esta prueba utiliza solo archivos de 1 MB para realizar una prueba ligera de carga directa desde el frontend:

| Métrica | Resultado |
| --- | ---: |
| Tamaño de Archivo | 1.0 MB |
| Estado | Éxito |
| Tiempo | 874 ms |
| Rendimiento | 1.14 MB/s |
| Mensaje de Error | Ninguno |

Resultados reales: la carga se completó con éxito y la página puede generar correctamente métricas de tiempo transcurrido y rendimiento.

> Los resultados de rendimiento pueden verse afectados por la red del navegador, la carga del clúster, los enlaces de proxy y la carga del almacenamiento de objetos. Una única prueba solo puede verificar la disponibilidad funcional; la aceptación formal del rendimiento debe probarse repetidamente en el mismo entorno y deben registrarse las estadísticas de P50, P95, y las tasas de fallo.

## 5. Precauciones
1. Confirme el Bucket de destino antes de realizar las pruebas para evitar escribir archivos de prueba en el bucket de almacenamiento incorrecto.
2. No muestre la Clave Secreta en texto plano en documentos o capturas de pantalla.
3. La carga directa desde el navegador depende del Punto de Acceso Público y de la configuración de origen cruzado.
4. S3Servicios compatibles con MinIO normalmente requieren habilitar el Estilo de Ruta.
5. Las pruebas de rendimiento generan tráfico de red real y escrituras en almacenamiento; evalúe el impacto en el entorno antes de probar archivos grandes.
6. La aceptación formal del rendimiento debe realizarse en múltiples rondas; un único resultado de prueba en el navegador no es suficiente.
