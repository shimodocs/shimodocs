# Búsqueda de Información de Archivos

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

La Consulta de información de archivos se utiliza para consultar los registros básicos de archivos en el sistema según el archivo interno GUID o el ID de archivo del lado del cliente, facilitando la verificación de identificadores de archivo, aplicaciones asociadas, tipos de archivos, estado y tamaño del contenido.

Esta página es solo de lectura y no modificará el contenido ni el estado del archivo.

## Accediendo a la página

Después de iniciar sesión en el backend de administración, seleccione **Consulta de información de archivos** en la navegación izquierda para acceder a la página.

## Consultando archivos

La página admite las siguientes condiciones de consulta:

- **Archivo interno GUID**: el archivo `history_guid`.
- **Proveedor de archivos del cliente GUID**: el lado del cliente `provider_file_id`.
- **ID de la aplicación**: opcional, se recomienda completar junto con el Proveedor GUID para especificar la aplicación asociada.

Se debe completar al menos uno de los campos Archivo interno GUID o Proveedor de archivos del cliente GUID y luego hacer clic en **Consultar**.

Si solo se completa el Proveedor GUID y no se ingresa el ID de la aplicación, el sistema devolverá todos los registros que coincidan con el Proveedor GUID, por lo que pueden aparecer múltiples resultados. 

### Obteniendo el archivo GUID 
1. En el caso de ShimoDocs Suite, solo necesita usar la dirección del conjunto en el navegador como el **Proveedor de archivos del cliente GUID**. 

## Resultados de la consulta

Los resultados de la consulta incluyen principalmente:

- **id**: La ID de clave primaria del registro de archivo.
- **app_id**: La ID de la aplicación asociada.
- **provider_archivo_id**: ID de archivo del lado del cliente.
- **history_guid**: Archivo histórico interno del sistema GUID.
- **created_at**: Hora de creación del registro.
- **type**: Tipo de archivo, como documento, hoja de cálculo, presentación, PDF, imagen o video.
- **created_by**: ID del usuario creador.
- **status**: El valor actual del estado del archivo.
- **archivo_content_tamaño**: Tamaño del contenido del archivo, en bytes.

Los tipos de archivo en los resultados mostrarán tanto el número de tipo como el nombre correspondiente para una identificación más fácil.

## Situaciones comunes

- **Aviso: Se deben ingresar las condiciones de consulta**: Por favor, complete al menos el archivo interno GUID o el Provider GUID.
- **Archivo no encontrado**: Por favor, verifique si el identificador está completo, o confirme si el archivo pertenece al entorno actual.
- **Se devolvieron múltiples registros**: El Provider GUID puede estar duplicado en varias aplicaciones; por favor, agregue la ID de la aplicación y busque nuevamente.
- **Tipo de archivo muestra como desconocido**: El número de tipo del registro actual puede no tener un nombre correspondiente configurado aún. Puede proporcionar el número al soporte técnico para confirmación.
- **No se puede determinar el valor del estado**: El campo de estado es un valor de registro subyacente y necesita un análisis adicional combinado con fenómenos comerciales específicos y registros.

> Los identificadores de archivo pertenecen a datos comerciales; por favor, evite compartirlos directamente en chats públicos o tickets externos.
