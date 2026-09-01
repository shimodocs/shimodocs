# Herramientas Generales

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Resumen de la página

La página de Herramientas Generales incluye 7 funciones de uso común: JSON formateo, conversión de formato, JWT decodificación, conversión de marcas de tiempo, verificación de tiempo de máquina, análisis de código QR y codificación/decodificación Base64.

1. Haga clic en cualquier tarjeta de función para entrar en la página de operación.
2. Después de entrar, puede cambiar directamente a otras herramientas desde la lista de funciones a la izquierda.
3. Haga clic en 'Volver al menú de funciones' para regresar a la página principal de tarjetas.

## 2. JSON Formateo

Esta función se utiliza para formatear, comprimir y validar JSON contenido.

1. Haga clic en "JSON Formateo."
2. Ingrese el contenido que se procesará en el "Entrada" JSONárea, por ejemplo:

   ```json
   {"name":"MDP","enabled":true,"items":[1,2]}
   ```

3. Haga clic en 'Formato', y se generará con sangría JSON a la derecha.
4. Haga clic en "Comprimir" y se generará un compacto JSON sin espacios ni saltos de línea a la derecha.
5. Haga clic en 'Copiar' para copiar el resultado procesado.
6. Haga clic en 'Borrar' para eliminar el contenido de entrada y salida.

Resultados de la prueba: Las cadenas, los valores booleanos y los arreglos se conservan correctamente, y las funciones de formato y compresión funcionan correctamente.

## 3. Conversión de Formato

Esta función admite la conversión y el formato entre JSON, YAML, y TOML formatos.

1. Haga clic en "Conversión de Formato."
2. Seleccione el formato de contenido de entrada en "Formato de Origen."
3. Seleccione el formato de salida deseado en "Formato de Destino."
4. Ingrese el contenido a convertir a la izquierda.
5. Haga clic en "Convertir," y el resultado se mostrará a la derecha.
6. Haga clic en "Intercambiar Formato" para cambiar los formatos de origen y destino.
7. Haga clic en "Formato" para ajustar la sangría y el diseño del contenido actual.
8. Haga clic en "Copiar" para copiar el resultado de salida.

Esta vez usamos JSON para convertir a YAML, ingrese:

```json
{"name":"MDP","ports":[80,443],"enabled":true}
```

Resultado de la conversión: 

```yaml
name: MDP
ports:
  - 80
  - 443
enabled: true
```

Resultados medidos: Campos, matrices y valores booleanos convertidos correctamente.

## 4. JWT Decodificar

Esta función se utiliza para analizar el Encabezado, la Carga útil y la Firma de un JWT Token.

1. Haga clic en "JWT Decodificar.
2. Pegue el JWT Token en el cuadro de entrada.
3. Haga clic en "Decodificar."
4. Vea el algoritmo de firma y el tipo de Token en el Encabezado.
5. Vea información como usuario, rol y tiempo de expiración en la Carga útil.
6. Vea el contenido sin procesar de la Firma.
7. Haz clic en el botón de copiar en cada sección para copiar los resultados del análisis.
8. Haz clic en "Borrar" para eliminar el Token actual y los resultados del análisis.

Resultados de la prueba: El Token de prueba analizó con éxito campos como `HS256`, `JWT`, usuario, rol y tiempo de expiración.

> JWT Decodificar es solo para ver la estructura del Token y no puede reemplazar la verificación de validez de la firma del lado del servidor.

## 5. Conversión de marcas de tiempo 

Esta función admite conversión bidireccional entre marca de tiempo Unix y fecha-hora. 

### 5.1 Conversión de marca de tiempo a fecha/hora 

1. Haz clic en "Conversión de marca de tiempo". 
2. Ingresa una marca de tiempo de 10 dígitos en segundos o 13 dígitos en milisegundos en "Marca de tiempo (segundos o milisegundos)". 
3. Haz clic en "Convertir" en la parte superior. 
4. Ve la fecha y hora en "Resultados de conversión". 
5. Haz clic en el botón de copiar junto al resultado para copiar el contenido. 

### 5.2 Fecha/hora a marca de tiempo 

1. Ingresa 'YYYY-MM-DD HH:mm:ss' o ISO el formato de hora en el campo "Fecha y hora". 
2. Haz clic en "Convertir" abajo. 
3. Ve la marca de tiempo Unix en "Resultado de conversión (segundos)". 
4. Haz clic en "Hora actual" para llenar rápidamente la marca de tiempo y fecha actuales. 
5. Haz clic en "Borrar" para limpiar todo el contenido. 

Resultado de prueba: '1704067200' se convirtió con éxito en fecha-hora, y la fecha-hora también se puede convertir correctamente a marcas de tiempo a nivel de segundos. 

> Al verificar datos entre zonas horarias, primero aclara si el tiempo de negocio utiliza UTC o zonas horarias locales. 

## 6. Verificación de hora de la máquina

Esta función se utiliza para verificar la hora de todos los Pods con `app=ws-gateway` en el actual NAMESPACE y resaltar las instancias con una desviación de tiempo de más de 30 segundos.

1. Haz clic en "Verificación de hora de la máquina".
2. Haga clic en "Actualizar" en la esquina superior derecha.
3. Verifique las etiquetas NAMESPACE y de consulta actuales.
4. Vea el tiempo de referencia calculado por el sistema, que es el tiempo medio de todos los Pods.
5. Vea el nodo donde se encuentra cada Pod, la marca de tiempo Unix y el tiempo legible.
6. Verifique "Diferencia con el Referente" y "Estado".
7. Si la desviación supera los 30 segundos, verifique la NTPconfiguración de tiempo de /Chrony, máquina virtual y zona horaria del nodo.

Resultado de la prueba: 1 `ws-gateway` Pod devuelto, con una desviación del tiempo de referencia de `0s` y estado "Normal".

## 7. Análisis de Código QR

Esta función se utiliza para subir imágenes de códigos QR y extraer el texto, enlaces u otro contenido que contengan.

1. Haga clic en "Análisis de Código QR".
2. Haga clic en "Seleccionar Archivo".
3. Elija una imagen clara de código QR desde su dispositivo local.
4. Después de que la página muestre la vista previa de la imagen, verifique el "Resultado del Análisis" a continuación.
5. Compare el resultado con el contenido esperado del código QR para confirmar la coherencia.
6. Haga clic en "Copiar" para copiar el contenido analizado.
7. Haga clic en "Borrar" para eliminar la imagen y el resultado del análisis.

Resultado de la prueba: El código QR de prueba se puede subir, previsualizar y analizar correctamente.

## 8. Codificación y Decodificación Base64

Esta función se utiliza para la conversión bidireccional entre texto plano y contenido Base64.

### 8.1 Codificación Base64

1. Haga clic en "Codificación y Decodificación Base64".
2. Ingrese texto plano en el lado izquierdo.
3. Haga clic en "Codificar".
4. Vea el resultado codificado en Base64 a la derecha.

### 8.2 Decodificación Base64

1. Ingrese contenido Base64 en el lado izquierdo.
2. Haga clic en "Decodificar."
3. Vea el texto restaurado a la derecha.
4. Haz clic en "Copiar" para copiar el resultado.
5. Haz clic en "Borrar" para limpiar la entrada y salida.

Resultado de la prueba:

```text
MDPTEST → TURQ5rWL6K+V
TURQ5rWL6K+V → MDPTEST
```

Chino UTF-8 el contenido se puede convertir de ida y vuelta normalmente.

