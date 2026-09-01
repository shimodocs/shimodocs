# Inspección de Middleware

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Resumen de Funciones 

La inspección de middleware se utiliza para verificar si los dependientes del sistema MySQL, Redis, Elasticsearch, S3, MongoDB, y Kafka pueden conectarse y leer/escribir normalmente, ayudándote a detectar anomalías en los servicios subyacentes a tiempo. 

La página soporta inspección instantánea, inspección programada, tendencias recientes de disponibilidad, registros históricos, así como notificaciones de fallos y recuperaciones. 

## Ingreso a la página 

Después de iniciar sesión en el backend de administración, seleccione **Inspección de Middleware** en la navegación a la izquierda para entrar en la página. 

La inspección de middleware está abierta solo a administradores. Si no ves este menú, por favor contacta al administrador del sistema para confirmar los permisos de tu cuenta. 

## Inspección instantánea 

En la **Resumen** página, haz clic **Inspección instantánea**, y el sistema realizará una inspección según los objetos de verificación guardados. 

Los resultados de la inspección pueden incluir los siguientes estados: 

- **Normal**: La conexión del componente y las operaciones de inspección se realizaron con éxito. 
- **Fallida**: El componente no se puede conectar, la lectura/escritura falló, o la respuesta es anormal. 
- **Omitido**: El componente no está configurado en el entorno actual, o no se cumplen las condiciones de inspección. 

Al hacer clic en los resultados del componente, puedes ver la dirección de destino, tiempo de respuesta y detalles del error. 

## Ver tendencias de disponibilidad 

La página de resumen muestra la disponibilidad reciente de cada componente basada en los resultados históricos de inspección. Permite ver los cambios de estado durante la última hora, 6 horas, 24 horas, 3 días, 7 días, 14 días o 30 días. 

Mueva el ratón sobre el período de tiempo para ver información como el número de inspecciones durante ese período, el tiempo de respuesta promedio y errores recientes. 

## Configurar Inspecciones Programadas 

En la **Programación y Alertas** en la página, puede configurar: 

- **Habilitar Inspecciones Programadas**: Una vez habilitado, el sistema se ejecutará automáticamente en los intervalos establecidos. 
- **Intervalo de Inspección**: Admite de 1 a 1440 minutos. 
- **Días de Retención del Historial**: Admite de 7 a 365 días; configurarlo en `0` significa que no habrá limpieza automática. 
- **Objetivos de Inspección**: Seleccione el middleware a inspeccionar. 
- **Canales de Notificación**: Elija los canales para recibir notificaciones de inspección. 
- **Notificar al Fallar**: Enviar una notificación cuando el estado general cambie de normal a anormal. 
- **Notificar al Recuperarse**: Enviar una notificación cuando un estado anormal vuelva a normal. 

Los cambios deben aplicarse haciendo clic en **Guardar**. Si aún no hay ningún canal de notificación, por favor vaya a la **Página de Canal de Notificación** para crear y habilitar un canal primero.

## Ver Historial de Inspección

En la **Historial** en la página, puede ver la hora de inspección, el método de activación, la duración de la ejecución y el estado final.

Los métodos de activación incluyen inspección manual e inspección programada. Haga clic en un registro para ver los resultados detallados de cada componente en esta inspección. 

## Situaciones comunes

- **No hay registros de inspección**: Primero puede hacer clic en **Inspeccionar Ahora**, o habilitar la inspección programada.
- **El componente muestra omitido**: Por favor confirme que el middleware correspondiente ha sido configurado y habilitado en el sistema.
- **Inspección fallida**: Verifique la red, la cuenta, la dirección de conexión y el estado del servicio de middleware según los detalles del error.
- **Notificación no recibida**: Por favor confirme que el canal de notificación ha sido seleccionado y habilitado, y verifique los interruptores de notificación de fallo o recuperación.
- **El aviso muestra que la inspección está en ejecución**: Solo se puede ejecutar una tarea de inspección al mismo tiempo, por favor espere a que la tarea actual termine e inténtelo de nuevo.

> Las inspecciones realizarán verificaciones ligeras de conexión o de lectura/escritura en el middleware; se recomienda establecer un intervalo de inspección razonable según la escala del entorno.
