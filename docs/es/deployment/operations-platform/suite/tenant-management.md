# Gestión de Inquilinos

[← ShimoDocs Suite Documentación de implementación](../../README.md)

> [!TIP]
>
> La Gestión de Inquilinos se utiliza para la gestión centralizada de inquilinos en ShimoDocs Suite.
> Los administradores pueden ver el número de inquilinos y el uso de asientos aquí, obtener credenciales de integración de terceros, gestionar configuraciones de IA, así como crear, editar, habilitar o deshabilitar inquilinos.
>

## 1. Accediendo a la Gestión de Inquilinos

1. Inicia sesión en **MDP Plataforma de Operaciones**.
2. Seleccionar **ShimoDocs Suite** en la parte superior.
3. Seleccionar **Gestión de Inquilinos** en la barra de navegación izquierda.

## 2. Entendiendo la Página de Gestión de Inquilinos

La información general del sistema actual se muestra en la parte superior de la página:

| Región | Descripción |
| --- | --- |
| API-KEY | Ver y copiar el `AppID` y `AppSecret` requerido para la integración de terceros. |
| Configuración de IA | Compruebe si las capacidades de IA están habilitadas y acceda a la configuración de modelos de IA y de búsqueda. |
| Número total de inquilinos | El número de inquilinos que se han creado en el sistema actual. |
| Inquilinos habilitados | El número de inquilinos actualmente habilitados. |
| Uso de asientos | Número de asientos usados, total de asientos en el sistema y proporción de uso. |

La lista de inquilinos a continuación muestra el nombre del inquilino, tiempo de activación, administrador del inquilino, uso de asientos y estado actual. En la columna 'Acciones', puede editar o deshabilitar el inquilino correspondiente.

## 3. Ver API Clave

`AppID` y `AppSecret` se utilizan para la autenticación cuando ShimoDocs Suite se integra con sistemas de terceros.

### Pasos de operación

1. Encuentra la **API-KEY** tarjeta en la página de gestión de inquilinos.
2. Haga clic en el ícono de copiar.
3. El sistema copiará el entorno actual `AppID` y `AppSecret`.
4. Ingrese las credenciales en la configuración correspondiente de la integración de terceros.

> `AppSecret` es una credencial sensible. Por favor, manténgala segura y no la escriba en documentos públicos, registros de chat o repositorios de código accesibles públicamente.

## 4. Gestión de la Configuración de IA

La tarjeta de configuración de IA muestra el estado de activación actual de la capacidad de IA.

Después de hacer clic en la tarjeta de configuración de IA, puede ver o editar el siguiente contenido en la página "Configuración del Modelo de IA y Búsqueda": 

### 1. Configuración Básica del Modelo

Se usa para configurar modelos de lenguaje grandes (LLMs) generales y sus modelos disponibles. En esta página, puede ver información como el proveedor, la clave de solicitud URL, API modelo predeterminado, ID del modelo, ventana de contexto y capacidades de entrada.

### 2. Configuración del Modelo de Imágenes

Se usa para configurar modelos de generación o edición de imágenes. En la página, puede ver el proveedor, nombre del modelo, Clave Base URL, API y capacidades de imagen soportadas.

### 3. Configuración del Motor de Búsqueda en Red

Se usa para configurar el servicio de búsqueda en red de IA. En la página, puede ver el proveedor del servicio, dirección de la interfaz, API Clave y tiempo de espera.

### 4. Configuración del Proveedor de Incrustaciones

Se usa para configurar el servicio de vectorización de texto. En la página, puede ver la Clave Base, URL, API Modelo de Incrustación y dimensiones del vector.

> Antes de modificar la configuración de la IA, por favor confirme primero que la dirección del servicio, API clave, ID del modelo y parámetros de capacidad son todos correctos. Después de la modificación, se recomienda usar una pequeña cantidad de contenido de prueba para verificar si la llamada al modelo funciona correctamente.

### 5. Uso de la IA en ShimoDocs Suite
Después de que se complete la configuración, puedes usar las funciones de IA dentro de ShimoDocs Suite.

## 5. Gestión de inquilinos existentes

En la lista de inquilinos, puede ver la información básica y el uso de asientos de cada inquilino.

### Editar inquilino

1. Encuentre al inquilino que necesita ajuste.
2. Haga clic en 'Editar' en la columna 'Acciones'.
3. Modifique la información del inquilino o el número de asientos según las indicaciones de la página.
4. Guarde los cambios y regrese a la lista para confirmar que la información ha sido actualizada.

### Desactivar o Restaurar Inquilino

- Para inquilinos actualmente habilitados, puede hacer clic en 'Desactivar' en la columna de 'Acciones'.
- Para restaurar un inquilino deshabilitado, vuelva a habilitarlo en los elementos de acción del inquilino correspondiente.

> Desactivar un inquilino afectará el acceso normal a ese inquilino. Por favor confirme que el inquilino objetivo es correcto antes de continuar, y programe la operación de acuerdo con el uso real.

## 6. Activando un Nuevo Inquilino

Antes de activar el inquilino, primero verifique el uso de asientos en la parte superior de la página para confirmar que todavía hay asientos disponibles para asignar.

### Pasos de Operación

1. Haga clic en "Activar Nuevo Inquilino" en la esquina superior derecha de la página.
2. Ingrese el nombre utilizado para identificar a este inquilino en "Nombre del Inquilino".
3. Confirme el correo electrónico del administrador de inquilino generado por el sistema. Después de que el inquilino se cree correctamente, guarde esta cuenta de administrador y la inicial PASSWORD de inmediato.
4. Verifique "Asientos Asignables" para comprender el número máximo de asientos que actualmente se pueden asignar al nuevo inquilino.
5. Ingrese el número de asientos asignados a este inquilino en "Asientos Asignados al Inquilino".
6. Después de confirmar que la información es correcta, haga clic en "Guardar".

### Descripción del Campo de Asiento

| Campo | Descripción |
| --- | --- |
| Asientos Asignables | El número máximo de asientos que actualmente puede asignar el sistema al inquilino. |
| Asientos Asignados al Inquilino | El número total de asientos asignados a este inquilino. Este número no puede ser menor que el número de asientos ya utilizados por el inquilino. |
| Asientos Utilizados por el Inquilino | El número de miembros activos de la empresa en este inquilino. Cada miembro activo ocupa un asiento. |

> Es necesario asignar un cierto número de asientos al crear un inquilino. El número de asientos se puede ajustar más tarde según el uso real.

## 7. Primer Inicio de Sesión y Cambio Inicial PASSWORD

Después de que el inquilino se cree correctamente, inicie sesión en ShimoDocs Suite utilizando la cuenta predeterminada generada por el sistema o la cuenta de administrador y cambie inmediatamente la inicial PASSWORD.

### 1. Abrir ShimoDocs Suite

Acceda en el navegador:

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS si ya está configurado, por favor visite: 

```text
https://<ACCESS_DOMAIN>/
```

### 2. Inicie sesión en ShimoDocs Suite

Ingrese la cuenta de administrador y la inicial PASSWORD creada al configurar el inquilino para completar el inicio de sesión.

### 3. Cambiar la Inicial PASSWORD

Después del primer inicio de sesión, siga las instrucciones de la página o la configuración de seguridad de la cuenta para cambiar la inicial PASSWORD. Una vez que la nueva PASSWORD está configurado, por favor manténgalo correctamente.

