# RDB Herramientas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

> [!TIP]
>
> RDB Se utiliza para ver y solucionar problemas de datos de bases de datos relacionales en la Plataforma de Operaciones y se usa comúnmente para confirmar registros de negocio, registros de configuración, estados de tareas, registros de operaciones y otros datos estructurados.
>
> Antes de usar, asegúrate de que tu cuenta actual tenga permisos de la herramienta de middleware y que se haya seleccionado el entorno de despliegue correcto.

> El RDB La herramienta accede directamente a los datos de la base de datos. Antes de consultar, confirma las tablas y condiciones de filtro para evitar consultas de alto costo o manipulación incorrecta de datos de producción.

## 1. Accediendo a RDB

1. Inicia sesión en **MDP Plataforma de Operaciones**.
2. Seleccionar **Servicios del sistema** en la parte superior.
3. Expandir **Herramientas de Middleware** en el panel de navegación izquierda.
4. Seleccionar **RDB**.

La página usualmente incluye áreas para conexión a la base de datos, selección de tablas, SQL entrada y resultados de la consulta.

## 2. Selección de una Conexión de Base de Datos

1. En la RDB página, selecciona la instancia de base de datos a la que necesitas acceder.
2. Confirma que el nombre de la instancia, la dirección de la base de datos o el identificador del entorno coincidan con el objetivo de solución de problemas actual.
3. Selecciona la base de datos objetivo.
4. Expande la lista de tablas y confirma que la tabla objetivo exista.

> Si la instancia de la base de datos está vacía o la conexión falla, primero verifique la configuración del middleware, los permisos de la cuenta y la conectividad de la red.

## 3. Visualización de la Estructura de la Tabla

1. Seleccione la tabla objetivo de la lista de tablas a la izquierda.
2. Vea los nombres de los campos, tipos de campos, claves primarias e información de índices.
3. Confirme las condiciones posteriores de la consulta en función del significado de los campos.

Se recomienda centrarse en la siguiente información:

| Información | Descripción |
| --- | --- |
| Clave Primaria | Se utiliza para consultar de manera precisa un solo registro. |
| ID de Negocio | Tales como ID del Inquilino, ID de Usuario, ID de Tarea, ID de Archivo. |
| Campo de Estado | Se utiliza para confirmar el estado actual del flujo del negocio. |
| Campo de Tiempo | Se utiliza para limitar el rango de tiempo de la consulta. |
| Campo Indexado | Preferiblemente se utiliza como filtro de consulta para reducir las exploraciones completas de la tabla. |

## 4. Uso de Común SQL

'Común' SQLse utiliza para ejecutar rápidamente consultas predefinidas, adecuado para escenarios de verificación frecuente como certificados, solicitudes, archivos y usuarios.

1. Haga clic **Común SQL** encima del SQL editor.
2. Seleccione el SQL Necesita usar desde la lista desplegable.
3. Si necesita revisar primero el contenido de la sentencia, haga clic **Vista previa** junto al correspondiente SQL.
4. Verifique la descripción, la base de datos, el nombre de la tabla y SQL el contenido en la ventana de vista previa.
5. Después de confirmar que todo es correcto, haga clic en **Ejecutar**.

Comúnmente utilizado SQL puede contener marcadores de posición:

| Marcador de posición | Tipo de Parámetro | Ejemplo |
| --- | --- | --- |
| `%s` | Cadena | ID de la aplicación, ID del archivo del proveedor, GUID histórico, ID de usuario proveedor |
| `%d` | Número | ID de Usuario Interno |

Si el SQL contiene marcadores de posición, un **Complete SQL Parámetros** La ventana se abrirá al ejecutarse.

1. Complete cada parámetro según el aviso.
2. Para los parámetros de cadena, complete el ID completo sin agregar comillas adicionales.
3. Complete los parámetros numéricos con números puros.
4. Haga clic **Ejecutar consulta**.

Los comúnmente usados SQL actualmente incluyen principalmente los siguientes escenarios:

| Escenario | Descripción |
| --- | --- |
| Consulta de certificados | Consultar certificados de aplicación e IDs de aplicación. |
| Consultar por appid especificado | Consultar detalles de la aplicación por ID de aplicación. |
| Consultar por guid de archivo de cliente especificado | Consultar detalles de archivo por `provider_file_id`. |
| Consultar por guid de archivo interno especificado | Consultar detalles de archivo por `history_guid`. |
| Consultar por ID de usuario interno especificado | Consultar detalles del usuario por ID de usuario interno. |
| Consultar por ID de usuario de cliente especificado | Consultar detalles del usuario por `provider_user_id`. |

> Incluso antes de usar comúnmente SQL, es necesario confirmar el entorno objetivo y los valores de los parámetros. Común SQL solo sirve para reducir el coste de la escritura manual y no garantiza que los resultados de la consulta cumplan los objetivos de esta investigación.

## 5. Ejecución de consultas

1. Rellene la declaración de la consulta en el SQL área de entrada.
2. Preferiblemente use `SELECT` consultas y no ejecute sentencias insert, update o delete.
3. La consulta predeterminada LIMIT es 10 y se puede ajustar manualmente.
4. Haga clic **Ejecutar consulta**.
5. Preferiblemente realice EXPLAIN, y **confirmar la ejecución** antes de iniciar la consulta.

Ejemplo:

```sql
SELECT *
FROM example_table
WHERE id = 'example-id';
```

## 6. Visualización de los resultados de la consulta

1. Vea los registros devueltos en el área de resultados.
2. Verifique si los campos clave cumplen con las expectativas.
3. Si el resultado está vacío, verifique la base de datos, el nombre de la tabla, las condiciones de la consulta y el rango de tiempo.
4. Si hay demasiados resultados, agregue condiciones de filtrado más precisas y consulte de nuevo.

## 7. Uso del Historial de Consultas

'Historial de Consultas' se utiliza para ver los SQL comandos que se han ejecutado en la página actual, lo que facilita reutilizar comandos de solución de problemas, verificar los resultados de la ejecución y copiar SQL."

> [!NOTE]
>
> El historial de consultas se guarda localmente en el navegador actual y no se almacenará de forma persistente. Cada dimensión de base de datos/tabla conserva hasta los 100 registros más recientes, y actualmente no hay expiración automática basada en el tiempo; borrar los datos del sitio del navegador, cambiar de navegador, cambiar de dispositivo o cambiar a una base de datos/tabla diferente hará que se vean registros históricos distintos.

1. Cambiar a **Historial de Consultas** en el área de resultados.
2. Vea el estado de ejecución, tiempo, base de datos, tabla, SQLnúmero de filas devueltas y tiempo transcurrido en los registros históricos.
3. Para ejecutar un SQL comando nuevamente, haga clic en **Insertar en el Editor y Ejecutar** en la columna de operaciones de ese registro.
4. Si solo necesita reutilizar el comando, haga clic en **Copiar SQL**.

Descripción de Campo del Historial de Consultas:

| Campo | Descripción |
| --- | --- |
| Estado | Si SQL se ejecutó correctamente; si falla, solucione en conjunto con los mensajes de error. |
| Tiempo | El tiempo de ejecución de la consulta actual. |
| Base de Datos | La base de datos seleccionada durante SQL la ejecución. |
| Tabla | La tabla asociada durante SQL la ejecución. |
| SQL | El comando de consulta realmente ejecutado. |
| Devuelve el número de filas | Número de filas de datos devueltas por esta consulta. |
| Tiempo Transcurrido | SQL El tiempo que tarda la ejecución y se puede usar para determinar si hay riesgo de consultas lentas. |
| Operación | Permite reinsertar y ejecutar, o copiar SQL. |

Al solucionar problemas con el historial de consultas, se recomienda enfocarse en: 

| Situación | Sugerencias de manejo |
| --- | --- |
| Estado fallido | Primero, verifique si el SQL sintaxis, la tabla de la base de datos existe y si los campos son correctos. |
| Toma mucho tiempo | Agrega condiciones de filtro, o primero verifica la estructura de la tabla y los campos del índice. |
| Devuelve demasiadas filas | Agregar 'WHERE' condición y 'LIMIT'. |
| Los resultados de múltiples consultas son inconsistentes | Confirme si la base de datos, la tabla o el entorno han sido cambiados. |

> El historial de consultas se utiliza para ayudar a revisar el proceso de resolución de problemas actual. Antes de volver a ejecutar el histórico SQL, aún debe confirmar el SQL contenido, la base de datos de destino y el entorno actual. 

## 8. Escenarios Comunes de Solución de Problemas

| Escenario | Sugerencias de operación |
| --- | --- |
| Confirmar si existen registros comerciales | Usar el ID del negocio para una consulta precisa. |
| Verifica el estado de la tarea | Consultar el campo de estado y la hora de actualización por ID de tarea. |
| Solucionar configuraciones ineficaces | Consultar el valor actual y la hora de actualización en la tabla de configuración. |
| Verificar cambios recientes | Consultar en orden descendente por el campo de tiempo y limitar el número de entradas devueltas. |
| Consultar información de la aplicación o del certificado | Preferir el uso de consultas de certificado o consultas de appid en “Común SQL”. |
| Reutilizar declaraciones de solución de problemas | Copiar SQL desde “Historial de Consultas”, verificar parámetros y ejecutar nuevamente. |

## 9. Precauciones

1. Las consultas incondicionales en tablas grandes están prohibidas en el entorno de producción.
2. Si no se está seguro sobre el SQL impacto, primero verificar en un entorno de bajo riesgo.
3. No corregir directamente los datos comerciales a través de RDB herramientas a menos que haya un plan de cambio claro y aprobación.
4. Parámetros en común SQL deben llenarse con los valores reales del entorno actual para evitar consultas incorrectas entre entornos.
5. SQL en el historial de consultas pueden contener identificadores sensibles, confirme el alcance antes de copiar o reenviar.
6. Cuando los resultados de la consulta involucren información sensible, no comparta externamente capturas de pantalla completas ni datos en texto plano.
