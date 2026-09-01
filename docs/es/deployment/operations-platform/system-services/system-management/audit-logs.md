# Registros de Auditoría

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

El registro de operaciones se utiliza para ver y rastrear las operaciones de gestión de usuarios en el sistema, lo que ayuda en la resolución de problemas, auditoría de seguridad y seguimiento de cambios.

Esta página es de solo lectura y no admite la modificación o eliminación de registros.

## Accediendo a la página

Después de iniciar sesión en el backend de administración, seleccione **Registro de Operaciones** en la navegación izquierda para acceder a la página.

## Filtrado de Registros

Puede consultar utilizando la siguiente combinación de condiciones:

- **Fuente del Evento**: Por ejemplo, Panel de Control, Centro de Configuración de Aplicaciones, Actualizador, Kubernetes Gestión de Recursos o Centro de Gestión de Usuarios.
- **Tipo de Operación**: Muestra las operaciones correspondientes según la fuente del evento, tales como actualizaciones de configuración, mejoras de versión, reinicios de servicio o acciones de gestión de usuarios.
- **Usuario Operativo**: Filtrar los registros generados por un usuario específico.

Después de seleccionar la fuente del evento, la lista de tipos de operación se ajustará automáticamente. Haga clic **Buscar** para aplicar las condiciones de filtrado, o haga clic en el botón de restablecer para borrar las condiciones.

## Visualizando la Lista de Registros

La lista muestra principalmente:

- ID del Registro.
- Fuente del evento y tipo de operación.
- Usuario operativo.
- Tipo, nombre e ID del objeto operado.
- Hora de la operación. 

El número total de registros se mostrará en la parte superior de la página, y la lista admite paginación y ajuste del número de elementos mostrados por página. 

## Ver Detalles del Registro 

Haga clic **Detalles** en el lado derecho del registro para ver la información completa, incluyendo: 

- El nombre y el identificador interno de la fuente y del tipo de operación. 
- El usuario que realiza la operación y el ID de usuario. 
- Hora de la operación. 
- Tipo de objeto, ID de objeto y nombre del objeto. 
- Metadatos del evento. 

Para cambios en el centro de configuración de la aplicación, los detalles también pueden mostrar las modificaciones de configuración, si ocurre un reinicio automático después de la liberación y las cargas de trabajo que se han reiniciado.

## Casos de uso comunes

- Verificar quién ejecutó un cambio de configuración específico.
- Confirmar la hora de actualizaciones del sistema, reinicios de servicio o operaciones de escalado.
- Rastrear cambios relacionados basados en nombres de objetos.
- Verificar diferencias de configuración y resultados de ejecución usando metadatos del evento.
- Investigar errores de operación o cambios administrativos inesperados.

## Situaciones comunes

- **No se encontraron registros**: Intente limpiar las condiciones del filtro, o confirme si la fuente seleccionada, el tipo de operación y el usuario coinciden.
- **La lista de tipos de operación está vacía**: Primero seleccione la fuente del evento, o recargue la página para obtener la enumeración más reciente.
- **La información del objeto está vacía**: Algunos eventos del sistema pueden no estar asociados con objetos específicos, lo cual es normal.
- **Los metadatos no están en contenido formateado**: Algunos eventos históricos pueden almacenarse como texto simple, y la página mostrará el contenido original.
- **La cantidad de registros no coincide con las expectativas**: Los registros solo documentan operaciones que han sido auditadas por el sistema, y pueden verse afectadas por la política de retención del entorno.

> Los registros de operación pueden contener usuarios, identificadores de objetos e información de cambios de configuración, y solo deben ser accesibles a personal autorizado.
