# Desplegar con MongoDB

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo introduce cómo deshabilitar el integrado MongoDB en el ShimoDocs y configurar el propio del cliente MongoDB como una base de datos de documentos de terceros. MongoDB Después de la configuración, el instalador verificará MongoDB la conectividad de red y permisos como conexión y autenticación. Una vez que los chequeos pasen, el despliegue puede continuar. 

# 1. Preparaciones Antes de la Configuración 
Antes de comenzar, por favor confirme: 
- MongoDB El servidor está instalado y funcionando normalmente. 
- El nodo de implementación puede acceder al host y puerto de la MongoDB Servidor. 
- Información de conexión y PASSWORD para autenticarse con el MongoDB Servidor están preparados. 

> [!TIP] 
> 
> La IP, el puerto y la cuenta en este artículo son ejemplos. Por favor, use la información del entorno real para la configuración y no registre la información real PASSWORD en documentos públicos o capturas de pantalla. 
> 

# 2. Ingresar a Configuración Avanzada 
En el paso de "Configuración" del instalador, después de completar la configuración de la red, el entorno objetivo y la información del nodo, expanda la "Configuración avanzada" en la parte inferior de la página. 

# 3. Cancele la instalación de la integrada MongoDB
En el área 'Servicios de Middleware', desmarque MongoDB

Después de desmarcar, el instalador ya no instalará el integrado MongoDB, y se utilizará una MongoDB previamente preparada externamente. Para otro middleware, si se deben usar los servicios integrados debe elegirse según el plan de implementación real.

# 4. Abrir Configuración de Middleware de Terceros
En el área de "Middleware de Terceros", haga clic en "Configurar".

# 5. Configurar MongoDB Base de Datos de Documentos
1. Seleccione "MongoDB Base de Datos de Documentos" a la izquierda.
2. Habilite "Usar Cola de Mensajes de Terceros". MongoDB Base de Datos de Documentos."
3. Ingresar Host, Puerto, USERNAME, PASSWORD, Sobrescribir Cadena de Conexión
4. Verifique y guarde

> [!WARNING]
>
> Atención: Si un tercero MongoDB crea una cuenta dedicada para la aplicación y sigue el 'principio de privilegio mínimo', donde una cuenta solo tiene permisos para acceder a una base de datos específica, es necesario asignar un usuario y PASSWORD para cada base de datos empresarial

# 6. Confirmar Resultados de Verificación
El instalador verificará lo siguiente:
- inicio de sesión: La cuenta puede ser autenticada normalmente
- conectividad: El entorno de despliegue puede acceder MongoDB
- permiso: La cuenta tiene permisos para conexión, autenticación, ejecución de comandos, etc.

Después de que todos los elementos de verificación muestren 'Éxito', cierre la ventana de configuración y regrese a la página 'Configuración' del instalador.

Si hay algún fallo, por favor verifique según las indicaciones en la página:
- Si el host y el puerto están llenados correctamente.
- Si la red entre el nodo de despliegue y el MongoDB Servidor conectado.
- Si USERNAME y PASSWORD son correctos.
- Si la cuenta tiene los permisos requeridos (conexión y autenticación, permisos de comando, etc.).

# 7. Continuar inicializando el despliegue
Después de regresar a la página 'Configuración', asegúrese MongoDB permanece sin marcar, haga clic en 'Inicializar Despliegue' para continuar completando la descripción general del despliegue, verificaciones y pasos de ejecución.

> [!TIP]
>
> Antes de inicializar el despliegue, por favor confirme una vez más que la MongoDB la configuración ha sido guardada y todos los elementos de validación han sido aprobados.
