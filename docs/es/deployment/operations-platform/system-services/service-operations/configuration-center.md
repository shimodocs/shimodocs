# Centro de Configuración

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Resumen de Funciones 

El Centro de Configuración se utiliza para ver y modificar configuraciones de aplicación de varios servicios. La página muestra tanto la configuración de plantilla de fábrica como la configuración actualmente activa, facilitando la comprensión de las diferencias de configuración y las modificaciones de liberación de manera controlada. 

Después de que se publique la configuración, el sistema guardará esta modificación y podrá reiniciar los servicios relevantes automáticamente según su selección para aplicar la nueva configuración. 

## Acceso a la Página 

Después de iniciar sesión en el backend de gestión, seleccione **Centro de Configuración** en la navegación izquierda para acceder a la página. 

El Centro de Configuración solo está disponible para administradores. Si no ve este menú, comuníquese con el administrador del sistema para confirmar los permisos de su cuenta. 

## Descripción de la Página 

La página se divide principalmente en tres áreas: 

- **Lista de Aplicaciones y Archivos**: Muestra archivos configurables por aplicación, con soporte para búsqueda por nombre de aplicación. 
- **Configuración de Plantilla de Fábrica**: Muestra la configuración original proporcionada en el paquete de instalación, solo para visualización y referencia. 
- **Configuración Activa Actualmente**: Muestra la configuración actualmente utilizada por el entorno, la cual puede ser editada directamente. 

Los archivos de configuración suelen estar en formato JSON, YAML, o TOML . Por favor, mantenga la sintaxis y estructura de datos correctas. 

## Modificación y Publicación de Configuración 

Se recomienda seguir los pasos a continuación: 

1. En el lado izquierdo, seleccione la aplicación y el archivo de configuración que necesita modificar.
2. Consulte la plantilla de fábrica y modifique el contenido de la configuración en el área de **Configuración Efectiva** .
3. Después de la modificación, la página mostrará el estado **Modificado pero no publicado**.
4. Haga clic **Modificado pero no publicado**, o use `Ctrl S` (Windows) / `Command S` (macOS) para abrir la ventana de confirmación.
5. Verifique las rutas de los campos, los tipos de cambio y los nuevos valores a publicar.
6. Elija si desea habilitar **Reinicie los servicios relacionados después de publicar la configuración** según sea necesario.
7. Haga clic **Publicar configuración** para completar la modificación.

Si hay errores de formato en el contenido de la configuración, el sistema mostrará un error y evitará la publicación. Por favor, corríjalo e inténtelo de nuevo.

## Confirmación de cambio

La ventana de confirmación antes de la publicación mostrará las diferencias de esta modificación:

- **Ruta**: La ruta de configuración que ha cambiado.
- **Operación**: Tipo de cambio, como agregar, modificar o eliminar.
- **Valor**: El valor de configuración después del cambio.

Se recomienda confirmar cada diferencia para evitar eliminar accidentalmente una configuración o modificar parámetros de servicio incorrectos.

## Reinicio del servicio

Algunas configuraciones solo surten efecto después de que se reinicia el servicio. Por defecto, la página habilita **Reinicie los servicios relacionados después de publicar la configuración**, y después de una publicación exitosa, los servicios asociados con la aplicación se reiniciarán automáticamente.

Si esta opción está desactivada, la configuración aún se publicará, pero los servicios relacionados pueden requerir un reinicio manual más tarde para aplicar la nueva configuración.

Durante el reinicio del servicio, las funciones relacionadas pueden experimentar breves fluctuaciones; se recomienda realizar cambios importantes en la configuración fuera de las horas pico.

## Situaciones comunes

- **Aplicación no encontrada**: Por favor, borre los criterios de búsqueda o confirme que la aplicación destino se haya implementado correctamente.
- **No se puede cargar el archivo de configuración**: Por favor, verifique el estado del servicio y los permisos de la cuenta actual antes de reintentar.
- **Error en el formato de configuración**: Por favor, revise la indentación, los corchetes, las comillas y el formato de los campos en JSON, YAML, o TOML.
- **No hay cambios para publicar**: El contenido real de la configuración no ha cambiado efectivamente, no se requiere publicación.
- **Cambios no efectivos después de la publicación**: Por favor confirme si los servicios relacionados se han reiniciado, y si es necesario, reinicie manualmente y verifique nuevamente.
- **Publicación fallida**: Por favor revise el contenido de la configuración o el estado del servicio según las indicaciones en la página, y vuelva a publicar después de manejarlo. 

> Los cambios en la configuración pueden afectar el inicio del servicio y las funciones del negocio, por favor publique solo después de confirmar completamente los cambios. 

## Ejemplo de interfaz de operación 

El diagrama a continuación muestra las áreas para seleccionar archivos de configuración, ver contenido de la configuración y editar. 

