# Registros en Tiempo Real

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción general de la función

Los registros en tiempo real se utilizan para ver los registros de operación de los servicios en un Kubernetes clúster en línea, permitiéndole localizar rápidamente anomalías de servicio, fallos de solicitud y retrasos en la ejecución.

Principales casos de uso:
- Filtrar rápidamente los registros que se activan en tiempo real
- Una alternativa ligera cuando no se ha desplegado un sistema completo de registro

Nota: Los registros en tiempo real se obtienen a través de Kubernetes API, y los datos de registro pueden verse afectados por Kubernetes actualizaciones continuas.

## Accediendo a la página

Después de iniciar sesión en la consola de gestión, seleccione **Registros en Tiempo Real** desde la navegación izquierda para entrar en la página.

Los registros en tiempo real solo son compatibles en el Kubernetes modo de implementación. Si no ve este menú, por favor contacte a su administrador del sistema para confirmar el modo de implementación y los permisos de acceso de su cuenta actual.

## Consultando Registros

Se recomienda seguir estos pasos:

1. Seleccione el **Clúster** y **NAMESPACE** que desea consultar.
2. Elija el objetivo del registro, admitiendo Deployment, StatefulSet o Pod, y se pueden seleccionar varios objetivos al mismo tiempo.
3. Seleccione el rango de registros, que puede consultar las 100 líneas más recientes, 1000 líneas, 5000 líneas, o registros desde el último 1 minuto hasta 24 horas.
4. Para reducir los resultados de la consulta, puede completar las condiciones de filtro a nivel de fila.
5. Haga clic **Iniciar** y la página cargará los registros dentro del rango seleccionado y mostrará continuamente los registros recién generados.

Haga clic **Detener** para finalizar la extracción en tiempo real. Cuando se reinicie la consulta, los registros en la página actual se borrarán y se cargarán nuevos resultados de consulta.

## Filtrado de Registros

El filtrado a nivel de fila no distingue mayúsculas de minúsculas para letras en inglés. Presione Enter después de ingresar las condiciones para aplicarlas. Los usos comunes son los siguientes:

```text
error
error AND timeout
error OR warning
error NOT health
error AND (timeout OR deadline)
"connection refused"
```

- `AND`: Incluye múltiples condiciones simultáneamente. 
- `OR`: Incluye cualquiera de las condiciones. 
- `NOT`: Excluye contenido especificado. 
- `()`: Combina múltiples condiciones de filtro. 
- `""`: Busca la frase completa que contiene espacios. 

Puede hacer clic en el botón de ayuda al lado derecho del cuadro de entrada para ver ejemplos completos de sintaxis. También puede usar **Consultas Comunes** para completar rápidamente objetivos de registro predefinidos y condiciones de filtro. 

## Visualización de Registros 

La lista de registros muestra la hora del registro, POD_NAME, y el contenido del registro. 

- Haga clic en POD_NAME para copiar el nombre completo. 
- Cuando el contenido es largo, puedes expandir para ver el registro completo. 
- Los registros en JSON formato pueden expandirse a contenido formateado y admiten copia con un solo clic. 
- Cuando hay muchos registros, la página se paginará automáticamente, y puedes saltar rápidamente al principio o al final usando los botones en la parte superior de la lista. 

## Distribución del volumen de registros 

El gráfico de distribución del volumen de registros en la página muestra el número de registros durante diferentes períodos y muestra el número total de líneas de registro y el número de líneas coincidentes después del filtrado. 

Puedes arrastrar para seleccionar un rango de tiempo en el gráfico de distribución, y la lista de registros solo mostrará el contenido dentro de ese rango de tiempo, lo cual es adecuado para enfocarse rápidamente en períodos de picos repentinos o anomalías en los registros. 

## Operaciones de página 

- **Iniciar**: Obtener registros según las condiciones actuales y recibir nuevos registros continuamente. 
- **Detener**: Detener la recepción de nuevos registros; los registros que ya se han cargado permanecerán en la página.
- **Limpiar**: Limpiar los registros que se muestran actualmente; si la obtención en tiempo real continúa, aparecerán nuevos registros.

## Situaciones comunes

- **Aún no hay registros**: Por favor, asegúrate de que el servicio objetivo esté en ejecución e intenta expandir el rango de tiempo de los registros.
- **No se seleccionó objetivo**: Por favor, selecciona al menos un Deployment, StatefulSet o Pod.
- **Demasiados objetivos**: Una sola consulta admite hasta 20 Pods reales; reduce la selección e inténtalo nuevamente.
- **Condiciones de filtro no válidas**: Por favor, verifica si `AND`, `OR`, `NOT`, paréntesis o comillas están completos.
- **Obtención de registros interrumpida**: Esto puede ser causado por un reinicio del servicio, cambios en la red o permisos insuficientes. Puedes hacer clic **Iniciar** de nuevo.

> La página conserva hasta 500,000 líneas de registros. Una vez que se supera el límite, los registros más antiguos se eliminarán automáticamente.

## Ejemplo de Interfaz de Operación

La figura a continuación muestra las áreas de selección de cargas de trabajo, filtrado por palabras clave y visualización de registros en tiempo real.

Haga clic **Seleccionar Clúster & NAMESPACE** para cambiar el clúster objetivo y NAMESPACE, luego continúe seleccionando las cargas de trabajo que desea ver.

