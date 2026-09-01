# Bloqueo de Colaboración

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

Cuando ocurre un retraso en Kafka, y se confirma que el retraso anormal es causado por un determinado archivo, puede usar esta función de bloqueo para prohibir la edición de ese archivo, solucionando así el problema de retraso de Kafka .

## Ilustración de Uso

1. Seleccione bloqueo colaborativo 

2. Ingrese el GUID del archivo, énfasis: esto se refiere al GUID dentro de ShimoDocs, no al ID de archivo del cliente 

Ingrese la ShimoDocs archivo GUID y haga clic en 'Agregar al Bloqueo'; el archivo será prohibido de editarse dentro de 3 minutos. 

Haga clic en el botón 'Desbloquear' para restaurar la funcionalidad de edición del archivo. 

### Cómo obtener el GUID 

1. Abra las herramientas de desarrollo del navegador 

2. Filtre las solicitudes pull 

3. En la solicitud, la cadena de 16 caracteres de rp3OMYnMrdcQJZkm es el GUID 

### Cómo Determinar el Efecto del Bloqueo

El documento no puede guardarse correctamente; después de editar el archivo, aparece un mensaje emergente offline después de 2 minutos, y se pierden los datos al actualizar la página.

### Cuándo Desbloquear

No se recomienda desbloquear. En general, esto se debe a que el archivo es demasiado grande para que el servidor admita su edición. Después de ser bloqueado, se convierte en solo lectura. Se recomienda copiar manualmente el contenido del archivo a uno nuevo.
