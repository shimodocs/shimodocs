# Gestión de Usuarios de la Suite

[← ShimoDocs Suite Documentación de implementación](../../README.md)

> [!TIP]
>
> La gestión de usuarios se utiliza para ver los usuarios en cada inquilino de ShimoDocs Suite y para habilitar o deshabilitar cuentas de usuarios en masa. 
>
> Para usar esta función, primero necesita seleccionar el inquilino donde se encuentra el usuario, luego ingresar la lista correspondiente de usuarios para la gestión. 

> La página de gestión de usuarios muestra el rango de inquilinos donde se encuentra el usuario usando el nombre del "Equipo". 

## 1. Ingresar a Gestión de Usuarios 

1. Inicia sesión en **MDP Plataforma de Operaciones**. 
2. En la parte superior, selecciona **ShimoDocs Suite**. 
3. En la barra de navegación izquierda, selecciona **Gestión de Usuarios**. 

## 2. Seleccione el equipo (inquilino) que desea gestionar 

Después de ingresar a la página de gestión de usuarios, el sistema mostrará primero la lista de equipos. Por favor seleccione el equipo (inquilino) al que pertenece el usuario de la lista. 

### Buscar Equipo (Inquilino)

1. En el cuadro de búsqueda en la parte superior de la página, ingrese el nombre del equipo, creador o ID. 
2. En los resultados de búsqueda, confirme el nombre del equipo, creador, número de activadores, capacidad y fecha de expiración. 
3. Haga clic en el nombre del equipo para ingresar a la lista de usuarios de ese equipo. 

### Descripción de Campos de la Lista de Equipos

| Campo | Descripción |
| --- | --- |
| ID | El identificador único del equipo en el sistema. |
| Nombre del Equipo | El nombre del equipo. Haga clic para ingresar a la lista de usuarios de este equipo. |
| Creador | La cuenta que creó este equipo. |
| Activado / Capacidad | El número de usuarios actualmente activados y los asientos disponibles para el equipo. |
| Fecha de Expiración | La fecha de expiración del servicio actual para este equipo. |

> Si el contenido de la lista no se actualiza a tiempo, puede hacer clic en "Actualizar" a la derecha del cuadro de búsqueda.

## 3. Ver Usuarios del Inquilino

Después de hacer clic en el nombre del inquilino, la página mostrará la lista de usuarios dentro de ese inquilino.

### Lista de Usuarios Descripción de Campos

| Campo | Descripción |
| --- | --- |
| ID | El identificador único del usuario en el sistema. |
| USERNAME | El nombre mostrado para el usuario en ShimoDocs Suite. |
| Correo electrónico | El correo electrónico vinculado a la cuenta del usuario. |
| Rol | El rol del usuario en el inquilino actual, como Administrador o Miembro. |
| Estado | Si la cuenta de usuario está actualmente habilitada. |

## 4. Búsqueda de Usuarios

Cuando hay muchos usuarios en el inquilino, puedes usar la función de búsqueda para encontrar rápidamente al usuario objetivo.

1. Ingrese la USERNAME, correo electrónico o ID de usuario en el cuadro de búsqueda sobre la lista de usuarios.
2. Verificar el USERNAME, correo electrónico y rol en los resultados de búsqueda para asegurarte de que has encontrado al usuario objetivo.
3. Para recargar la lista, haz clic en “Actualizar” en el lado derecho del cuadro de búsqueda.

## 5. Habilitar Usuarios en Masa

Cuando una cuenta de usuario está deshabilitada y necesita ser restaurada, puedes habilitar usuarios en masa.

### Pasos

1. Confirma que la página actual muestra el inquilino objetivo.
2. Marca uno o más usuarios para habilitar en el lado izquierdo de la lista de usuarios.
3. Confirma que el número mostrado como “Seleccionados” en la esquina superior derecha de la página sea correcto.
4. Haz clic en “Habilitar en Masa”.
5. Confirma la operación según los mensajes de la página.
6. Después de que la operación se complete, actualiza la lista y confirma que el estado del usuario haya cambiado a "Habilitado."

## 6. Deshabilitar Usuarios en Lote

Cuando los usuarios temporalmente no necesitan usar ShimoDocs Suite, sus cuentas pueden ser deshabilitadas en lote.

### Pasos

1. Confirma que la página actual muestra el inquilino objetivo.
2. Marca uno o más usuarios para deshabilitar en el lado izquierdo de la lista de usuarios.
3. Confirma que el número mostrado en "Seleccionados" en la esquina superior derecha de la página sea correcto.
4. Haz clic en "Deshabilitar en Lote."
5. Confirma la operación según los mensajes de la página.
6. Después de que se complete la operación, actualice la lista y confirme que el estado del usuario se haya actualizado.

> Deshabilitar usuarios afectará el uso normal de ShimoDocs Suite para esa cuenta. Por favor, verifique el inquilino, USERNAME, el correo electrónico y el rol antes de operar para evitar afectar por error a otros usuarios.

## 7. Volver a la Lista de Inquilinos

Si necesita cambiar a otro inquilino:

1. Haga clic en "Volver a la Lista de Equipos" en la parte superior izquierda de la página.
2. Busque y seleccione nuevamente el inquilino objetivo en la lista de inquilinos.
