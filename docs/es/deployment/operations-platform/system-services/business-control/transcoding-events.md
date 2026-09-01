# Búsqueda de Eventos de Transcodificación

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción general de la función 
La función de consulta de eventos de transcodificación se utiliza para verificar rápidamente los eventos de transcodificación recientes en el MDP backend, ayudando a localizar y solucionar problemas durante el proceso de transcodificación. 

Por defecto, la lista mostrará los eventos de transcodificación más recientes. 

## tarea_Adquisición de ID
Una tarea_ID se genera durante las tareas de importación y exportación

Abra el modo desarrollador del navegador. Al exportar, puede obtener el ID de la tarea_verificando esta interfaz como se muestra en la figura a continuación

## Consulta por tarea_id
Ingrese la tarea_id en el cuadro de entrada taskID para filtrar rápidamente los eventos de transcodificación relacionados con esa tarea.

## Ver enlace
Como se muestra en la figura a continuación, haga clic en el icono "Ver enlace" en el registro de la fila para ver todos los eventos relacionados con la tarea_id, lo que facilita analizar el proceso completo de esta tarea de transcodificación de principio a fin.

## Localización de excepciones

### gRPC Exitoso, devolución de llamada no recibida
Si gRPC se envía correctamente y se recibe una respuesta con éxito, indica que la tarea de transcodificación ha sido enviada al servicio de transcodificación. En este caso, si la devolución de llamada no se recibe a tiempo debido a un tiempo de espera del servicio de transcodificación, es necesario investigar el servicio de transcodificación.

### Devolución de llamada recibida
Si puede ver que hay una devolución de llamada para la tarea_id, entonces generalmente se considera un fallo de transcodificación, como formatos incompatibles u otras excepciones.
