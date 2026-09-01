# Redis Herramientas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

> [!TIP]
>
> Redis se utiliza en la Plataforma de Operaciones para ver Redis instancias, bases de datos, una lista de Keys, y detalles de las Keys. Se usa comúnmente para solucionar problemas de cachés, sesiones, bloqueos distribuidos, contadores de limitación de velocidad y estados de corto plazo.
>
> La página soporta búsqueda por Key o prefijo de Key, y muestra el tipo de Key, TTLy el valor actual.

## 1. Accediendo a Redis

1. Inicia sesión en **MDP Plataforma de Operaciones**.
2. Seleccionar **Servicios del sistema** en la parte superior.
3. Expandir **Herramientas de Middleware** en la barra de navegación izquierda.
4. Seleccionar **Redis**.

El lado izquierdo de la página es el área de consulta de Key, y el lado derecho es el área de detalles de Key.

## 2. Selección Redis Instancia y DB

1. En el primer menú desplegable en la esquina superior izquierda, seleccione la Redis instancia.
2. En el segundo menú desplegable, seleccione la DB, por ejemplo `db0`.
3. La página cargará la lista de Keys basada en la instancia y DB actuales.

Si la lista de DB está vacía o la página reporta un error, por favor primero verifique si la Redis configuración de la instancia es normal.

## 3. Buscar Key

1. Ingrese el nombre de la Key o el prefijo de la Key en el cuadro de búsqueda.
2. Haga clic en el botón de búsqueda o presione Enter para ejecutar la consulta.
3. Vea la lista de Keys en el lado izquierdo.
4. Si necesita recargar la lista bajo las condiciones actuales, haga clic en el ícono de actualización.

El mensaje del cuadro de búsqueda es "Por favor ingrese el nombre de la key, se admite búsqueda difusa." La página mostrará el tipo de Key coincidente y TTL.

## 4. Ver lista de Keys

La lista de Keys contiene la siguiente información:

| Información | Descripción |
| --- | --- |
| Tipo | El Redis tipo de la Clave, como `string`, `hash`, `list`, `set`, `zset`. |
| Nombre de la Clave | La Clave completa actualmente coincidente. |
| TTL | El tiempo restante de expiración de la Clave; la página muestra "permanente" si la Clave actual no tiene expiración. |

## 5. Ver Detalles de la Clave

1. Haga clic en la Clave objetivo en la lista de Claves a la izquierda.
2. El área de detalles a la derecha muestra el nombre de la Clave, tipo, TTLy valor específico.
3. Para actualizar los detalles de la Clave actual, haga clic en el botón de actualizar en el área del título de detalles.

Los diferentes tipos de métodos de visualización son los siguientes:

| Tipo | Método de Visualización |
| --- | --- |
| `string` | Mostrar el valor completo en un cuadro de texto. |
| `hash` | Mostrar el campo Clave y Valor en una tabla. |
| `list` / `set` | Mostrar la lista de elementos en una tabla. |
| `zset` | Mostrar Puntuación y Miembro en una tabla. |

## 6. Copiar Valores de Campos

1. Busque el campo o valor que desea copiar en la tabla de detalles de la Clave.
2. Haga clic en el contenido correspondiente.
3. La página copiará ese contenido al portapapeles.

> `string` El tipo se muestra en un cuadro de texto y se puede copiar directamente seleccionando el texto; los tipos de tabla permiten hacer clic en el valor para copiar.

## 7. Escenarios Comunes de Solución de Problemas

| Escenario | Acción Sugerida |
| --- | --- |
| Confirme si la caché existe | Después de seleccionar la instancia y la DB, busque por Clave completa o prefijo. |
| Verifique si la caché ha expirado | Verifique el TTL en la lista de Claves o detalles. |
| Ver campos Hash | Haga clic en la Clave para ver los campos y valores en la tabla a la derecha. |
| Ver datos ordenados de ZSet | Haga clic en la `zset` Clave para ver Puntuación y Miembro. |
| Revise el estado más reciente de la misma Clave | Haga clic en el botón de actualizar en el área de detalles. |
