# Desplegar con Dameng V8

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo explica cómo deshabilitar el incorporado MySQL en el ShimoDocs instalador y configurar Dameng DM8 como una base de datos relacional de terceros. Después de completar la configuración, el instalador verificará el inicio de sesión en la base de datos, la conectividad de red y los permisos de creación de tablas. Una vez aprobadas las verificaciones, la implementación puede continuar.

## 1. Preparativos Antes de la Configuración

Antes de comenzar, confirme por favor:

- Dameng DM8 está instalado y funcionando normalmente.
- El nodo de implementación puede acceder al host y puerto de la Dameng Base de Datos.
- El host de la base de datos, el puerto, USERNAME, y PASSWORD están listos.
- La cuenta de la base de datos tiene los permisos para iniciar sesión, conectarse, crear tablas y eliminar tablas.
- Dameng La base de datos ha completado la MySQL configuración del modo de compatibilidad según lo requerido. Para instrucciones detalladas, por favor consulte ["Dameng Guía de Configuración de Integración de Base de Datos](requirements.md).

> [!TIP]
>
> La IP, el puerto y la cuenta en este artículo son solo ejemplos. Por favor utilice la información del entorno actual para la configuración, y no registre la información real PASSWORD en documentos externos o capturas de pantalla.

## 2. Ingresar en Configuración Avanzada

En el paso 'Configuración' del instalador, después de completar la configuración de red, entorno objetivo e información del nodo, expanda la 'Configuración Avanzada' en la parte inferior de la página.

## 3. Deseleccionar Instalación Incorporada MySQL

En la sección "Servicios de Middleware", deseleccione **MySQL**.

Después de deseleccionar, el instalador ya no instalará el MySQLincorporado, y usará la Dameng Base de Datos preparada de ahora en adelante. Para otros middleware, si se deben usar servicios incorporados debe seleccionarse según el plan de implementación real.

## 4. Abrir Configuración de Middleware de Terceros

En el área 'Middleware de Terceros', haga clic en 'Configurar'.

## 5. Configurar Dameng Base de Datos

1. Seleccione "RDB Base de Datos Relacional" en la izquierda.
2. Habilite "Usar Base de Datos Relacional de Terceros."
3. En la sección "Dialectos", seleccione **DM (Dameng)**.
4. Rellene la información de conexión de la base de datos.

| Elemento de configuración | Descripción |
| --- | --- |
| Host | La dirección IP o nombre de host accesible de la Dameng Base de Datos |
| Puerto | El puerto de escucha de la Dameng Base de datos, típicamente 5236 por defecto, sujeto a la configuración real |
| USERNAME | La cuenta utilizada para conectarse a la base de datos |
| PASSWORD | El PASSWORD correspondiente a la cuenta de la base de datos |
| DSN | Generado automáticamente por el instalador basado en la información anterior, no se requiere ingreso manual |

5. Después de confirmar que la información es correcta, haga clic en "Validar y Guardar."

## 6. Confirmar Resultados de Verificación

El instalador comprobará los siguientes elementos:

- **inicio de sesión**: La cuenta de la base de datos puede iniciar sesión normalmente.
- **conectividad**: El entorno de implementación puede acceder a la base de datos.
- **permiso para crear tablas**: La cuenta de la base de datos tiene permiso para crear y eliminar tablas.

Después de que todas las verificaciones muestren "Éxito", cierre la ventana de configuración y regrese a la página de "Configuración" del instalador.

Si hay algún elemento fallido, verifique según las indicaciones de la página:

- Si el host y el puerto están llenados correctamente.
- Si la red entre el nodo de implementación y la base de datos está conectada.
- Si el USERNAME y PASSWORD son correctos.
- Si la cuenta de la base de datos tiene los permisos requeridos.
- Si el Dameng El servicio de base de datos y MySQL las configuraciones de compatibilidad han tenido efecto normalmente.

## 7. Continuar Inicializando la Implementación

Después de regresar a la página de "Configuración", confirme que MySQL permanece sin marcar, luego haga clic en "Inicializar Implementación" para continuar completando la visión general de implementación, las verificaciones y los pasos de ejecución.

> [!TIP]
>
> Antes de inicializar la implementación, confirme nuevamente que la Dameng configuración se haya guardado y que todos los elementos de verificación hayan pasado.
