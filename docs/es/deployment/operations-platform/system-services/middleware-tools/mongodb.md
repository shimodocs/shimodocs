# MongoDB Herramientas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

> [!TIP]
>
> MongoDB se utiliza en la Plataforma de Operaciones para ver MongoDB bases de datos, colecciones y contenidos de documentos. Es adecuado para solucionar problemas de datos basados en documentos, estados intermedios, registros de tareas y datos comerciales con estructuras flexibles.
>
> La página admite la búsqueda por base de datos o colección, y después de seleccionar una colección, MongoDB JSON se pueden usar consultas condicionales.

## 1. Accediendo a MongoDB

1. Inicia sesión en **MDP Plataforma de Operaciones**.
2. Seleccionar **Servicios del sistema** en la parte superior.
3. Expandir **Herramientas de Middleware** en la barra de navegación izquierda.
4. Seleccionar **MongoDB**.

El lado izquierdo de la página muestra el árbol de base de datos y colección, mientras que el lado derecho muestra las condiciones de la consulta y los resultados de la consulta.

## 2. Búsqueda de Bases de Datos o Colecciones

1. Ingrese DATABASE_NAME o palabras clave del nombre de la colección en el cuadro de búsqueda en la parte superior izquierda.
2. Ver la lista de árbol filtrada.
3. Borre el cuadro de búsqueda para restaurar la visualización de todas las bases de datos.

## 3. Expandir la base de datos y seleccionar una colección

1. Encuentre la base de datos objetivo en el árbol a la izquierda.
2. Haga clic en el ícono de expansión a la izquierda de la base de datos para cargar la lista de colecciones.
3. Seleccione la colección objetivo.
4. La página a la derecha realizará automáticamente una consulta una vez con la condición predeterminada. `{}`.

> Seleccionar solo la base de datos no ejecutará una consulta de colección; primero debe seleccionar una colección específica, y luego se mostrará el área de consulta a la derecha.

## 4. Complete las Condiciones de la Consulta

1. información de conexión del MongoDB JSON condiciones de la consulta en el cuadro de entrada de consulta a la derecha.
2. Seleccione el número de resultados a devolver, compatible `limit: 10`, `limit: 20`, `limit: 50`.
3. Haga clic **Consultar**.

Ejemplo de consulta: 

```json
{
  "_id": "task-123"
}
```

Ejemplo de consulta por campo:

```json
{
  "status": "running"
}
```

## 5. Visualización de los resultados de la consulta

1. Después de completar la consulta, verifique los documentos devueltos en el área de resultados a la derecha.
2. Por defecto, los resultados se muestran en JSON formato.
3. Haga clic **Expandir** para expandir el documento actual.
4. Haga clic **Colapsar** para colapsar el documento actual.
5. Haga clic **Copiar** para copiar el documento actual JSON.

## 6. Escenarios comunes de solución de problemas

| Escenario | Sugerencia operativa |
| --- | --- |
| Confirma si el documento existe | Después de seleccionar la colección, consulta usando `_id` o el ID del negocio. |
| Verifica el estado de la tarea | Consulta por ID de tarea y verifica el campo de estado y el campo de hora de actualización. |
| Encuentra un tipo de registro | Usa una combinación de campos como estado, tipo y hora de creación para consultar. |
| Resultado vacío | Verifica si se seleccionaron la base de datos, la colección, los nombres de campo y los tipos de campo correctos. |
| Necesitas llevar los resultados de solución de problemas | Haz clic **Copiar** en un solo resultado. |
