# Desplegar con MySQL 8

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo explica cómo deshabilitar el instalador incorporado MySQL en el ShimoDocs instalar y configurar su propio MySQL como una base de datos relacional de terceros. Después de la configuración, el instalador verificará el inicio de sesión en la base de datos, la conectividad de red y los permisos de creación de tablas. Una vez verificado, se puede proceder con la implementación. 

# 1. Preparación antes de la configuración 
Antes de comenzar, por favor confirme: 
- MySQL 8.0 está instalado y funcionando normalmente. 
- El nodo de implementación puede acceder al MySQL host y puerto de la base de datos. 
- Host de la base de datos, puerto, USERNAME, y PASSWORD están preparados. 
- La cuenta de la base de datos tiene permiso para iniciar sesión, conectarse, crear tablas y eliminar tablas. 

> [!TIP]
>
> La IP, el puerto y la cuenta en este artículo son ejemplos. Por favor, configure usando la información real de su entorno; no registre los reales PASSWORD en documentos externos o capturas de pantalla. 
>

# 2. Ingresar a Configuración Avanzada 
En el paso "Configuración" del instalador, después de completar la configuración de red, entorno objetivo y nodo, expanda la sección "Configuración Avanzada" en la parte inferior de la página. 

# 3. Cancele la instalación de la integrada MySQL
En el área 'Servicios de Middleware', desmarque MySQL.

Después de desmarcar, el instalador ya no instalará el integrado MySQL, y un externo MySQL Se utilizará 8.0 que ya está preparada más adelante. Si otros middleware usan servicios integrados debe elegirse según el plan de implementación real.

# 4. Abrir Configuración de Middleware de Terceros
En la sección "Middleware de Terceros", haga clic en "Configurar".

# 5. Configurar MySQL Base de Datos
1. Seleccione 'RDB Base de Datos Relacional' en la izquierda.
2. Habilite 'Usar Base de Datos Relacional de Terceros'.
3. Seleccione Estándar MySQL bajo 'Dialect'.
4. Rellene la información de conexión de la base de datos.
5. Verifique y guarde.

# 6. Confirmar Resultados de Verificación
El instalador verificará lo siguiente:

- inicio de sesión: Si la cuenta de la base de datos puede iniciar sesión normalmente.
- Conectividad: Si el entorno de implementación puede acceder a la base de datos.
- permiso para crear tablas: Si la cuenta de la base de datos tiene permiso para crear y eliminar tablas.

Después de que todos los elementos de verificación muestren 'Éxito', cierre la ventana de configuración y regrese a la página 'Configuración' del instalador.

Si hay algún fallo, por favor verifique según las indicaciones en la página:
- Si el host y el puerto están llenados correctamente.
- Si la red entre los nodos de implementación y la base de datos está conectada.
- Si USERNAME y PASSWORD son correctos.
- Si la cuenta de la base de datos tiene los permisos requeridos.

# 7. Continuar inicializando el despliegue
Después de regresar a la página 'Configuración', asegúrese MySQL permanece sin verificar, haga clic en 'Inicializar Implementación' para continuar completando la visión general, verificación y pasos de ejecución de la implementación.

> [!TIP]
>
> Antes de inicializar la implementación, confirme una vez más que la MySQL configuración 8.0 ha sido guardada y que todos los elementos de validación han sido aprobados.
