# gRPC Herramientas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

> [!TIP]
>
> El gRPC la herramienta se utiliza para conectarse a servicios internos gRPC , ver Servicios y Métodos, e iniciar llamadas de depuración del método Unary.
>
> La página admite tres formas de seleccionar un objetivo: ingresar la dirección manualmente, seleccionar por Kubernetes Servicio o seleccionar por Pod.

## 1. Accediendo a gRPC

1. Inicie sesión en la **MDP Plataforma de Operaciones**.
2. Seleccionar **Servicios del sistema** en la parte superior.
3. Expandir **Herramientas de Middleware** en la barra de navegación izquierda.
4. Seleccionar **gRPC**.

La página muestra primero el área "Objetivo" para seleccionar el gRPC servicio al que conectarse.

## 2. Métodos de Selección de Objetivo

La página proporciona tres modos de objetivo:

| Modo | Descripción |
| --- | --- |
| Manual | Ingrese manualmente la gRPC dirección, por ejemplo, `svc-user:50051`. |
| Servicio | Seleccione el objetivo por Clúster, Espacio de Nombres, Servicio y Puerto. |
| Pod | Seleccione objetivos por clúster, Espacio de Nombres, Pod y Puerto. |

## 3. Conexión Manual 

1. Seleccionar **Manual**. 
2. Ingrese la gRPC dirección del servicio en el **cuadro de entrada Dirección.**  
3. Haga clic **Conectar**. 
4. Después de una conexión exitosa, la página entrará al área de trabajo de depuración de Servicio / Método. 

## 4. Conectarse por Servicio

1. Seleccionar **Servicio**.
2. Seleccione el clúster y el Espacio de Nombres objetivo.
3. En el **Servicio / Puerto** desplegable, seleccione el servicio y puerto objetivo.
4. Si la lista de servicios no se actualiza, haga clic en **Actualizar**.
5. Haga clic **Conectar**.

## 5. Conectarse por Pod

1. Seleccionar **Pod**.
2. Seleccione el clúster y el Espacio de Nombres objetivo.
3. En el **Pod / Puerto** desplegable, seleccione el Pod y puerto objetivo.
4. Si la lista de Pods no se actualiza, haga clic **Actualizar**.
5. Haga clic **Conectar**.

## 6. Seleccionar Servicio y Método

Después de una conexión exitosa, la página se divide en una lista de Servicios, una lista de Métodos, un área de solicitud y un área de respuesta.

1. Seleccione el servicio objetivo de la lista de Servicios a la izquierda.
2. Puede usar el cuadro de búsqueda de Servicio para filtrar servicios.
3. Seleccione el método objetivo de la lista de Métodos.
4. Opciones de filtro de métodos conmutables: `Unary`, `Streaming`, `All`.
5. Puede usar el cuadro de búsqueda de Método para filtrar métodos.

> La página actual solo admite llamar métodos Unarios. Los métodos de transmisión aparecerán como no disponibles.

## 7. Rellenar Parámetros de Solicitud

El área de solicitud admite dos formas de rellenar:

| Método | Descripción |
| --- | --- |
| Modo Formulario | La página genera un formulario basado en los campos de entrada del método. |
| JSON Modo | Cuando **JSON Modo** está habilitado, edite directamente el JSON cuerpo completo de la solicitud. |

Pasos para usar el Modo Formulario:

1. Seleccione el Método objetivo.
2. Rellene los parámetros de solicitud campo por campo.
3. Use el menú desplegable para seleccionar campos enumerados.
4. Seleccionar `true` o `false` para campos booleanos.
5. Use comas como se indica en la página para campos repetidos.

Pasos para usar JSON Modo:

1. Encienda el **JSON Modo** interruptor.
2. Rellene el JSON completo en el cuadro de texto.
3. Asegúrese de que el JSON formato sea válido.

## 8. Rellenar Metadatos

1. Expandir **Metadatos** en el área de solicitud.
2. Rellene la Clave y el Valor.
3. Para agregar varias entradas de Metadatos, haga clic en **Agregar**.
4. Para eliminar una fila, haga clic en el ícono de eliminar.

Los metadatos se utilizan comúnmente para transmitir información de autenticación, ID de solicitud o contexto empresarial.

## 9. Inicie la llamada y vea la respuesta

1. Confirme el objetivo, Servicio, Método, cuerpo de la solicitud y Metadatos.
2. Haga clic **Invocar** en la parte superior derecha de la página.
3. Vea el estado, tiempo transcurrido, Metadatos de la respuesta y la respuesta JSON en el área de respuesta.
4. Si la llamada falla, el área de respuesta mostrará el estado de error y el contenido del error.

## 10. Cambiar o Reconectar Objetivo

1. Haga clic **Conectar** en la parte superior para recargar la definición del servicio del objetivo actual.
2. Haga clic **Cambiar Objetivo** para regresar a la página de selección de objetivos.
3. Después de cambiar de objetivo, necesita reconectar y seleccionar Servicio / Método nuevamente.

## 11. Escenarios Comunes de Solución de Problemas

| Escenario | Sugerencia de Operación |
| --- | --- |
| Verifique si el servicio es conectable | Seleccione el objetivo y haga clic **Conectar** para ver si se puede cargar la lista de Servicios. |
| Encuentre métodos de interfaz | Use la búsqueda de Servicios y el filtrado de búsqueda de Métodos. |
| Depurar interfaz de consulta | Seleccione un método Unary, complete los parámetros de solicitud y haga clic **Invocar**. |
| Necesita pasar el contexto | Expanda Metadatos y complete la Clave y el Valor correspondientes. |
| La respuesta está vacía o falló | Verifique el estado de la respuesta, el contenido del error y los Metadatos. |
