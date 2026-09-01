# Reparación de Documentos

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

Los síntomas de que un archivo esté dañado de manera anormal incluyen que el documento no se abra correctamente, aparezcan ventanas de error durante la carga y que el contenido no se muestre.

Cuando no se puede abrir el documento, use esta función para reparar el archivo.

# Ilustración de Uso

Hay 2 métodos de reparación.

## Preparativos

### Referencia rápida para tipos de archivos

|**tipoDeArchivo**|**Archivo URL Características de la dirección**|**Observaciones**|
|:----|:----|:----|
|rdoc(richdoc)|/**docs**/{fileguid}|Documento ligero|
|mosheet(modoc)|/**hojas**/{fileguid}|Tabla|
|modoc(modoc)|/**docx**/{fileguid}|Documento profesional|

### Advertencia de riesgo operativo

El fallo de reparación no tiene riesgo

## Recuperación de datos cifrados

Solo admite la reparación de archivos tipo tabla. Para otros tipos de archivos, seleccione [Reparar desde datos históricos]

Este es el método preferido. Puede entrar directamente al archivo GUID para realizar la reparación. Esto GUID es el ShimoDocs archivo GUID.

El principio de la reparación es convertir los datos de archivo cifrados en el almacenamiento de objetos en datos de contenido de archivo no cifrados, lo cual es aplicable a la mayoría de los escenarios.

Si este método falla en la reparación, elija otro método.

### Archivo GUID

1. Abra las herramientas de desarrollo del navegador

2. Filtrar solicitudes pull

3. En la solicitud, la parte rp3OMYnMrdcQJZkm, esta cadena de 16 caracteres, es el guid

## Restaurar desde datos históricos

Restaurar desde registros históricos

1. ID de archivo del cliente

2. Tipo de archivo

   1. Para documentos/hojas de cálculo/presentaciones tradicionales, elija modoc

   2. Para documentos ligeros, elija richdoc

1. Seleccionar fuente de datos

### ID de archivo del cliente

Si el cliente usa ShimoDocs para todo el sitio, es la dirección del archivo del documento en el navegador, por ejemplo, el siguiente m8AZMoYMrRsYbOkb

### Cómo determinar la fuente de datos 

Verifique la configuración del servicio svc-edit 

Elemento de configuración: history.driver 

Si es mysql, el interruptor 'Usar fuente de datos Mongo' está apagado 

Si es mongo, el interruptor 'Usar fuente de datos Mongo' está encendido 

