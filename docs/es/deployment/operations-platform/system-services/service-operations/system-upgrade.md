# Actualización del Sistema

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

La actualización del sistema se utiliza para cargar y aplicar un nuevo MDP paquete de instalación. Antes de la actualización oficial, el sistema comprobará automáticamente el paquete de actualización y el entorno actual, y mostrará los cambios de configuración y servicio involucrados en esta actualización, ayudándole a completar la actualización de versión o el mantenimiento rutinario.

La página también conserva el historial de actualizaciones, lo que facilita ver los registros de actualizaciones pasadas, el estado de ejecución y los registros relacionados.

**Nota**: Las actualizaciones de versión principal pueden actualizar el esquema de la base de datos. La actualización implica cambios en la configuración, reinicios de servicios y cambios en la interfaz funcional, lo que puede afectar la experiencia del usuario. Debe realizarse durante las horas de menor actividad.

## Ingrese en la Página

Después de iniciar sesión en el backend de administración, seleccione **Actualización del Sistema** en la navegación izquierda para acceder a la página.

Las actualizaciones del sistema solo están disponibles para administradores. Si no ve este menú, póngase en contacto con su administrador del sistema para confirmar los permisos de la cuenta actual.

## Preparativos Antes de la Actualización

Antes de iniciar la actualización, se recomienda confirmar los siguientes puntos:

- Utilice el paquete de actualización proporcionado oficialmente que coincida con el tipo de producto y el método de implementación actuales.
- El paquete de actualización se encuentra en `.tar.gz` formato. Por favor, no extraiga ni modifique los archivos dentro del paquete.
- Se recomienda realizar la actualización durante horas de menor actividad empresarial o ventanas de mantenimiento.
- Confirme que el servicio actual esté funcionando normalmente y notifique a los usuarios relevantes con antelación.
- Si la actualización implica un cambio de licencia, prepare el contenido de la nueva licencia con antelación.

## Pasos de Actualización

### 1. Subir el Paquete de Actualización

Haga clic en el área de carga en la página de actualización del sistema, o arrastre el paquete de actualización al área de carga. Después de completar la carga, el sistema analizará y verificará automáticamente el paquete de actualización.

La verificación incluye principalmente:

- El formato y la integridad del contenido del paquete de instalación.
- Si la firma del paquete de instalación es válida.
- La versión del paquete de actualización y el plan de actualización.
- Si el tipo de producto y la arquitectura de implementación coinciden.
- Si la licencia actual admite esta actualización.

Los resultados de la verificación se dividen en los siguientes estados:

- **Aprobado**: La verificación es normal y puede continuar.
- **Cambio**: Hay cambios esperados en esta actualización; por favor, confirme el contenido antes de continuar.
- **No Coincide**: Hay problemas que impiden la actualización; necesita reemplazar el paquete de actualización o manejar la configuración relacionada antes de volver a subirlo.

### 2. Ingresar Licencia

Si el sistema determina que esta actualización requiere una licencia actualizada, la página mostrará los productos que necesitan actualización y mostrará información como el código de máquina del servidor actual.

Después de pegar el contenido de la nueva licencia, haga clic en **Verificar y Guardar Temporalmente**. La actualización solo puede continuar una vez que la Licencia haya sido verificada con éxito. La Licencia guardada temporalmente entrará en efecto automáticamente después de que la actualización se haya aplicado correctamente. Para referencia de la Licencia, consulte [Gestión de Licencias]

Si la página muestra que la Licencia no necesita ser actualizada, puede proceder directamente al siguiente paso.

### 3. Confirmar el contenido del paquete de actualización

La página mostrará los archivos de configuración y los recursos de servicio en el paquete de actualización. Puede seleccionar archivos específicos para ver su contenido y confirmar que el paquete de actualización es consistente con el objetivo de actualización actual.

### 4. Confirmar cambios

El sistema comparará el entorno actual con el paquete de actualización y mostrará los recursos que se añadirán, modificarán, eliminarán o reiniciarán en esta actualización.

Por favor preste especial atención para confirmar: 

- Si hay alguna eliminación de recursos inesperada. 
- Si algún servicio importante necesita ser reiniciado. 
- Si los cambios en los archivos de configuración son los esperados. 

### 5. Aplicar actualización 

Después de confirmar que la información anterior es correcta, haga clic en **Confirmar para iniciar la actualización**. El sistema creará un snapshot previo a la actualización y comenzará a aplicar el paquete de actualización. 

Durante la actualización, la página mostrará continuamente los registros de ejecución, incluyendo información como actualizaciones de recursos, reinicios de servicios y comprobaciones del estado de ejecución. Cuando algunos componentes se reinicien, la página de gestión puede estar temporalmente inaccesible. Por favor espere un momento y luego vuelva a abrir la página para verificar el progreso. 

Si la ejecución falla, puede resolver los problemas de acuerdo con los registros y luego hacer clic en **Reaplicar**. 

### 6. Completar la actualización 

Después de que la tarea de actualización se ejecute con éxito, haga clic en **Completar** para finalizar este proceso de actualización. 

La página de finalización de la actualización mostrará el nombre y la versión del paquete de actualización y proporcionará las siguientes operaciones: 

- **Ver Registro de Ejecución**: Ver el proceso completo de esta actualización. 
- **Revertir a la Versión Pre-Actualización**: Ingrese en la instantánea antes de la actualización y siga las indicaciones de la página para revertirla. 
- **Volver a Actualización de Aplicación**: Volver a la página principal de actualización del sistema. 

## Historial de Actualizaciones 

Al final de la página principal de actualización del sistema se mostrará el historial de actualizaciones, incluyendo el nombre del paquete de actualización, versión, fecha de creación y estado de ejecución. 

Haga clic en el registro de actualización para volver a entrar en el proceso correspondiente y ver el progreso de la actualización o los resultados históricos de ejecución.

## Situaciones comunes

- **La verificación del paquete de actualización falló**: Por favor, confirme que la fuente del paquete de actualización, la integridad del archivo, el tipo de producto y la arquitectura de despliegue son correctos.
- **Incompatibilidad de versión**: Por favor, verifique la versión actual del sistema y la versión del paquete de actualización para asegurar que se está utilizando la ruta de actualización correcta.
- **Se requiere actualización de licencia**: Obtenga una nueva licencia compatible con la versión de destino y el entorno en ejecución actual, valídela y guárdela temporalmente antes de continuar.
- **Página del proceso de actualización temporalmente no disponible**: El MDP servicio puede estar actualizándose o reiniciándose, por favor espere un momento y actualice la página.
- **La tarea de actualización falló**: Revise los registros de ejecución para identificar la causa; después de resolver el problema, use **Reaplicar**.
- **Anomalía del servicio después de la actualización**: Primero revise los registros de ejecución y el estado del servicio; si se necesita una recuperación, puede revertir usando una instantánea previa a la actualización.

> Las actualizaciones del sistema modificarán las configuraciones de los servicios y pueden provocar reinicios de los servicios. Por favor, proceda solo después de confirmar que el paquete de actualización y los cambios son correctos.
