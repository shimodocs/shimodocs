# Configuraciones Avanzadas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

La configuración avanzada se utiliza para gestionar las personalizaciones del sistema `pd-config` directamente a través de YAML. Esto es adecuado para manejar parámetros avanzados que no se proporcionan en la página de configuración estándar o para configuraciones masivas.

El sistema combinará las configuraciones personalizadas con las configuraciones predeterminadas de fábrica. Los valores personalizados en la misma ruta sobrescribirán los valores predeterminados, mientras que las configuraciones que no se llenen continuarán usando los valores predeterminados de fábrica.

## Ingresar a la Página

Después de iniciar sesión en el backend de administración, seleccione **Configuraciones Avanzadas** en la navegación del lado izquierdo para acceder a la página.

Los Ajustes Avanzados solo están disponibles para administradores. Esta página puede afectar a todo el sistema, por lo que debe ser operada por personal familiarizado con la MDP estructura de configuración.

## Descripción de la Página

La página está dividida en dos partes:

- **pd-config Predeterminado de fábrica**: La configuración predeterminada proporcionada por el paquete de instalación, solo lectura.
- **pd-config Personalizado**: La configuración personalizada del cliente actualmente guardada, que puede ser editada.

La configuración personalizada no necesita copiar todo el contenido predeterminado; normalmente, solo se conservan los elementos de configuración que necesitan ser sobrescritos o añadidos.

## Modificar y Publicar la Configuración

Se recomienda seguir estos pasos:

1. Haga clic **Actualizar** para asegurarse de que se cargue la configuración personalizada más reciente.
2. Comparar con la configuración predeterminada de fábrica a la izquierda, y modificar el YAML contenido a la derecha.
3. Haga clic **Publicar**.
4. Comprobar el contenido agregado, eliminado y modificado en la ventana de confirmación de diferencias.
5. Usar los botones de diferencia anterior/siguiente para revisar los cambios ítem por ítem.
6. Después de confirmar que no hay errores, haga clic **Confirmar Publicación**.

Después de una publicación exitosa, el sistema creará una tarea de aplicación de configuración y abrirá el registro de la tarea en una nueva ventana. Dependiendo de los cambios y la configuración del sistema, los servicios relacionados pueden reiniciarse automáticamente.

## Historial de Configuración

Haga clic **Historial** para ver configuraciones personalizadas publicadas anteriormente, incluyendo ID de registro, hora de creación y MD5.

- Haga clic **Ver** para ver el completo YAML de una versión histórica.
- Después de seleccionar dos registros, puede realizar una comparación de diferencias.

La página actual no proporciona un botón de restauración con un solo clic. Para restaurar configuraciones históricas, por favor verifique la versión correspondiente, compruebe el contenido, cópielo manualmente en el área de edición y vuelva a publicar.

## Notas

- YAML la sintaxis debe mantenerse correcta; preste atención a la sangría, los dos puntos y los tipos de datos.
- No elimine arbitrariamente elementos de configuración que no entienda.
- Antes de publicar, revise completamente las diferencias para evitar sobrescribir cambios recientemente enviados por otros administradores.
- Se recomienda realizar cambios importantes durante las horas comerciales de menor actividad, y debe registrarse la configuración original con antelación.
- Después de publicar, revise el registro de la tarea para asegurarse de que la aplicación de la configuración y la verificación del estado del servicio se hayan completado.

## Situaciones comunes

- **Error de Publicación**: Por favor verifique YAML el formato, los nombres de los campos y los tipos de valores de configuración.
- **Reinicio de Servicio Después de Publicar**: Los cambios de configuración pueden requerir reiniciar los servicios relacionados, lo cual es normal.
- **Página Temporalmente Inaccesible Después de Publicar**: MDP o los servicios relacionados pueden estar reiniciándose; por favor actualice después de esperar un momento.
- **La Configuración No Logra el Efecto Esperado**: Por favor, confirme que la ruta de configuración es correcta y verifique el resultado final combinado y el registro de tareas.
- **Modificación de Configuración Incorrecta**: Encuentre la versión correcta en el historial, copie el contenido y vuelva a publicar.

> La configuración avanzada afectará configuraciones a nivel de sistema y operaciones de servicio, no publique directamente configuraciones no verificadas en el entorno de producción.

## Ejemplo de Interfaz de Operación

La figura a continuación muestra la interfaz de edición comparativa entre la configuración predeterminada de fábrica y la configuración personalizada.

