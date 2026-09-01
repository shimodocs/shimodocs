# Desplegar con Kafka

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo explica cómo deshabilitar el instalador incorporado Kafka en el ShimoDocs y configurar el propio del cliente Kafka como una cola de mensajes de terceros. Después de la configuración, el instalador verificará Kafkala conectividad de red y el permiso para crear temas. Una vez verificado, la implementación puede proceder. 

# 1. Preparaciones Antes de la Configuración 
Antes de comenzar, por favor confirme: 
- Kafka El servidor está instalado y funcionando normalmente. 
- El nodo de implementación puede acceder al Kafka host y puerto del servidor. 
- Información de usuario de autenticación preparada y PASSWORD para conectarse al Kafka Tema del Servidor (si el clúster externo Kafka ha habilitado la autenticación segura). 
- La cuenta autenticada debe usar un usuario administrador y tener permiso para crear, eliminar, autorizar y leer y escribir Temas (si el externo Kafka ha habilitado la autenticación segura). 

> [!TIP]
>
> La IP, el puerto y la cuenta en este artículo son ejemplos. Por favor, configure usando la información real de su entorno; no registre los reales PASSWORD en documentos externos o capturas de pantalla. 
>

# 2. Ingresar a Configuración Avanzada 
En el paso "Configuración" del instalador, después de completar la configuración de red, entorno objetivo y nodo, expanda la sección "Configuración Avanzada" en la parte inferior de la página. 

# 3. Cancelar la Instalación del Integrado Kafka
En el área 'Servicios de Middleware', desmarque Kafka.

Después de desmarcar, el instalador ya no instalará el integrado Kafka, y un externo Kafka lo que se ha preparado se utilizará más tarde. Para otros middleware, si se deben usar servicios integrados debe elegirse de acuerdo con el plan de despliegue real.

# 4. Abrir Configuración de Middleware de Terceros
En el área de "Middleware de Terceros", haga clic en "Configurar".

# 5. Configurar Kafka Middleware de Mensajería
## Kafka Servidor SASL Autenticación No Habilitada
1. Seleccione "Kafka Cola de Mensajes" a la izquierda.
2. Habilite "Usar Cola de Mensajes de Terceros". Kafka Rellene la
3. información de conexión del Kafka Servidor.
5. Verifique y guarde

## Habilitar SASL Autenticación en Kafka Servidor 
Si Kafka Servidor ha SASL autenticación habilitada, debe habilitarse simultáneamente en la interfaz web: Habilitar solo cuando el broker requiera acceso autenticado Botón 
1. Habilitar SASL mecanismo de 
2. autenticación 
3. Ingrese USERNAME y PASSWORD 
4. Verifique y guarde 

# 6. Confirmar Resultados de Verificación
El instalador verificará lo siguiente:
- inicio de sesión: La cuenta puede autenticarse normalmente (si SASL está habilitado).
- conectividad: El entorno de despliegue puede acceder Kafka.
- Permiso para crear Tema: La cuenta tiene permisos para crear Temas, autorizar y leer/escribir.

Después de que todos los elementos de verificación muestren 'Éxito', cierre la ventana de configuración y regrese a la página 'Configuración' del instalador.

Si hay algún fallo, por favor verifique según las indicaciones en la página:
- Si el host y el puerto están llenados correctamente.
- Si la red entre el nodo de despliegue y el Kafka servidor está conectada.
- Si USERNAME y PASSWORD son correctos (Kafka Servidor ha habilitado SASL autenticación).
- Si la cuenta tiene los permisos requeridos (Kafka Servidor ha SASL autenticación habilitada).

# 7. Continuar inicializando el despliegue
Después de regresar a la página 'Configuración', asegúrese Kafka permanece sin supervisión, luego haga clic en 'Inicializar Despliegue' para continuar con los pasos de visión general, verificación y ejecución del despliegue.

> [!TIP]
>
> Antes de inicializar el despliegue, por favor confirme una vez más que la Kafka configuración ha sido guardada y todos los elementos de verificación han pasado.
