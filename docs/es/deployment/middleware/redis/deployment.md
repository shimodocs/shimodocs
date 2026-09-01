# Desplegar con Redis

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo explica cómo deshabilitar el incorporado Redis en el ShimoDocs y configurar el propio del cliente Redis como una base de datos de caché de terceros. Después de la configuración, el instalador verificará Redis la conectividad de red, la conexión, la autenticación, la ejecución de comandos, los permisos de publicación/suscripción, etc. Una vez que se superen las comprobaciones, se puede continuar con la implementación. 

# 1. Preparación antes de la configuración 
Antes de comenzar, por favor confirme: 
- El Redis El servidor está instalado y funcionando normalmente. 
- Los nodos de implementación pueden acceder al Redis host y puerto del Servidor. 
- Información del usuario de autenticación y PASSWORD para conectarse a Redis Servidor están preparados. 

> [!TIP] 
> 
> La IP, el puerto y la cuenta en este artículo son ejemplos. Utilice la información del entorno real para la configuración y no registre contraseñas reales en documentos públicos o capturas de pantalla. 
> 

# 2. Ingresar a Configuración Avanzada 
En el paso "Configuración" del instalador, después de completar la configuración de la red, el entorno de destino y la información del nodo, expanda la "Configuración Avanzada" en la parte inferior de la página. 

# 3. Cancelar la instalación integrada Redis
En la sección 'Servicios de Middleware', desmarque Redis

Después de desmarcar, el instalador ya no instalará el integrado Redis, y usará el middleware externo preparado Redis en su lugar. Si otros middleware utilizan servicios integrados, debe elegirse según el plan de implementación real.

# 4. Abrir Configuración de Middleware de Terceros
En el área 'Middleware de Terceros', haga clic en 'Configurar'.

# 5. Configurar Redis Middleware de Caché
## Redis Servidor de Nodo Único
1. Seleccione "Redis Caché" en la izquierda.
2. Habilite "Usar Cola de Mensajes de Terceros". Redis".
3. Haga clic en "Nodo Único"
4. Ingresar Host, Puerto, PASSWORD
5. Verifique y guarde

## Redis Clúster Sentinela del Servidor
1. En el lado izquierdo, seleccione 'Redis Caché'.
2. Habilite 'Usar Terceros Redis'.
3. Haga clic en 'Clúster Sentinela'.
4. Ingrese 'Nombre del Maestro, SENTINEL PASSWORD, SENTINEL Nodos'.
5. Verifique y guarde

# 6. Confirmar Resultados de Verificación
El instalador verificará lo siguiente:
- inicio de sesión: La cuenta puede ser autenticada normalmente
- conectividad: El entorno de despliegue puede acceder Redis
- permiso: La cuenta tiene permisos para conectarse, autenticarse, ejecutar comandos, publicar/suscribirse, etc.

Después de que todos los elementos de verificación muestren 'Éxito', cierre la ventana de configuración y regrese a la página 'Configuración' del instalador.

Si hay algún fallo, por favor verifique según las indicaciones en la página:
- Si el host y el puerto están llenados correctamente.
- Si la red entre el nodo de despliegue y el Redis servidor está conectada.
- Si USERNAME y PASSWORD son correctos.
- Si la cuenta tiene los permisos requeridos (conexión y autenticación, permisos de comando, permisos de publicación/suscripción, etc.).

# 7. Continuar inicializando el despliegue
Después de regresar a la página 'Configuración', asegúrese Redis permanece sin supervisión, luego haga clic en 'Inicializar Despliegue' para continuar con los pasos de visión general, verificación y ejecución del despliegue.

> [!TIP]
>
> Antes de inicializar la implementación, confirme una vez más que la Redis la configuración ha sido guardada y todos los elementos de verificación han sido aprobados.
