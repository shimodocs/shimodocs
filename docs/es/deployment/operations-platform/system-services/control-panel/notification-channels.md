# Canales de Notificación

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

Los canales de notificación se utilizan para gestionar de manera centralizada cómo se reciben los mensajes de alerta del sistema, permitiendo que la inspección de middleware y otras funciones envíen notificaciones de fallos y recuperaciones.

Los canales actualmente soportados incluyen WeCom, DingTalk, Feishu y Webhooks personalizados.

## Accediendo a la página

Después de iniciar sesión en la consola de administración, seleccione **Canales de Notificación** en la navegación izquierda para acceder a la página.

Los canales de notificación solo están disponibles para administradores. Si no ve este menú, por favor contacte al administrador del sistema para confirmar los permisos de su cuenta.

## Creando un Nuevo Canal de Notificación

Haga clic **Crear Canal**, ingrese el nombre del canal y seleccione el tipo de canal:

- **WeCom**: Ingrese la Key de Webhook del robot.
- **DingTalk**: Ingrese el Webhook completo URL, y opcionalmente ingrese el Secret de la firma según la configuración del robot.
- **Feishu**: Ingrese el Webhook completo URL, y opcionalmente ingrese el Secret de la firma según la configuración del robot.
- **Webhook Personalizado**: Ingrese el URL, HTTP método de solicitud y la plantilla del cuerpo.

Confirme si desea habilitar el canal y luego haga clic en **Guardar**.

## Webhook Personalizado

La plantilla del cuerpo de un Webhook personalizado admite las siguientes variables: 

```text
{{title}}
{{body}}
{{level}}
```

Ejemplo de plantilla predeterminada: 

```json
{"title":"{{title}}","body":"{{body}}","level":"{{level}}"}
```

Cuando el sistema envíe una notificación, reemplazará las variables con el título, contenido y nivel de alerta reales. 

## Canal de Prueba 

Después de guardar, haga clic **Probar** en el lado derecho del canal. El sistema enviará un mensaje de prueba para verificar si la dirección del Webhook, la firma y la conexión de red son correctas. 

Se recomienda probar inmediatamente después de crear o modificar un canal, antes de vincularlo a la inspección de middleware u otras funciones comerciales. 

## Habilitar, Editar y Eliminar 

- **Habilitar/Deshabilitar**: Ajuste el estado de habilitación al editar el canal. Cuando esté deshabilitado, el canal no recibirá notificaciones comerciales. 
- **Editar**: Puede modificar el nombre del canal, el tipo y la configuración del Webhook. 
- **Eliminar**: Elimine los canales que ya no se usan. Los canales referenciados por las inspecciones de middleware deben desvincularse antes de poder eliminarlos. 

## Situaciones comunes

- **Fallo al Enviar Prueba**: Por favor, verifique la dirección del Webhook, la Clave, el Secreto, HTTP el método y los permisos de acceso a la red.
- **Error al Guardar**: Por favor, asegúrese de que todos los campos requeridos estén completos y que el formato del Webhook URL sea correcto.
- **Alertas Comerciales No Recibidas**: Por favor, confirme que el canal está habilitado y que ha sido seleccionado en la página comercial correspondiente.
- **No se Puede Eliminar el Canal**: Este canal aún puede estar siendo usado por inspecciones de middleware. Por favor, elimine la asociación y guarde la configuración de inspección primero.
- **Formato de Contenido Recibido por Webhook Personalizado Incorrecto**: Por favor, verifique si la plantilla del Cuerpo cumple con los requisitos del sistema de destino.

> La dirección del Webhook y la firma del Secreto son información sensible. Por favor, limite el acceso y evite compartirlo públicamente mediante capturas de pantalla, registros o herramientas de chat.

## Interfaz de Operación de Ejemplo

La figura a continuación muestra los tipos de canal y el formulario de configuración al crear un nuevo canal de notificación.

