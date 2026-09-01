# Gestión de Usuarios de la Plataforma

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción de la Función

La gestión de usuarios del sistema se utiliza para mantener las cuentas de usuario en el MDP gestión de backend, incluyendo la creación de usuarios, edición de información básica, restablecimiento PASSWORD, administrar la autenticación de dos factores y eliminar usuarios.

## Accediendo a la página

Después de iniciar sesión con una cuenta de administrador del sistema, seleccione **Gestión de Usuarios del Sistema** en la navegación izquierda para acceder a la página.

Este menú solo es accesible para las cuentas designadas de administradores del sistema. Si no ve este menú, por favor contacte a su administrador del sistema.

## Ver Usuarios

La página muestra los apodos de los usuarios, NOMBRES DE USUARIO, roles, direcciones de correo electrónico, información de contacto, últimas horas de inicio de sesión y tiempos de registro, y proporciona información general como el total de usuarios, usuarios recientemente activos y cuentas de administradores.

Puedes ver todos los usuarios a través de la paginación de la lista.

## Crear Nuevo Usuario

Haga clic **Crear Nuevo Usuario** y completa la siguiente información:

- **Apodo**: Requerido, se utiliza para mostrar en la página.
- **USERNAME**: Requerido, se utiliza para iniciar sesión en el sistema.
- **Correo electrónico**: Requerido, debe proporcionar una dirección de correo electrónico válida.
- **Información de Contacto**: Opcional.
- **Rol**: Elige usuario ordinario o administrador.

Después de la creación, el sistema generará un inicial PASSWORD. Por favor cópialo inmediatamente y proporciónalo al usuario de manera segura, ya que el PASSWORD puede no ser visible nuevamente una vez que se cierre esta ventana.

### Descripción del Rol de Usuario

- Administrador
  - Puede usar todas las páginas basadas en permisos globales
    - ShimoDocs Suite
    - Centro de Documentos
    - Servicios del sistema
- Usuario Regular
  - Alcance de uso de la página basado en permisos globales
    - ShimoDocs Suite
    - Centro de Documentos
    - Servicios del Sistema (oculto)

## Editar Información del Usuario

Haz clic en el botón de edición a la derecha del usuario para modificar su apodo, correo electrónico e información de contacto. USERNAME no se puede modificar en esta página después de la creación.

## Restablecer PASSWORD

Después de hacer clic en el botón Restablecer PASSWORD y confirmar la operación, el sistema generará un nuevo PASSWORD. El original PASSWORD quedará inmediatamente inválido.

Por favor, copie y guarde adecuadamente el nuevo PASSWORD, entréguelo al usuario correspondiente a través de un canal confiable, y recuerde al usuario iniciar sesión y cambiar el PASSWORD lo antes posible.

## Administrar Autenticación de Dos Factores

- **Habilitar o Deshabilitar 2FA**: Use el interruptor en la fila del usuario y continúe en la ventana de confirmación.
- **Restablecer 2FA**: El sistema generará un nuevo código QR y Secreto, y la información de verificación original quedará inválida.

Después de restablecer, los usuarios deben usar autenticadores como Authenticator para volver a escanear y vincular. Los códigos QR y los Secretos son credenciales sensibles y no deben transmitirse por canales públicos. 

Vincular 2FA

Agregar escaneando con Authenticator, y use el 2FA dinámico de 6 dígitos para los próximos inicios de sesión

## Eliminar Usuario

Después de hacer clic en el botón de eliminar y confirmar, la cuenta del usuario será eliminada. La acción de eliminar no se puede deshacer, así que asegúrese de que la cuenta ya no esté en uso y complete la entrega de datos y permisos necesarios antes de ejecutar.

## Situaciones comunes

- **No se puede crear el usuario**: Por favor, verifique si el USERNAME está duplicado, si el formato del correo electrónico es correcto y si todos los campos obligatorios están completos.
- **El usuario no puede iniciar sesión**: Verifique que USERNAME y PASSWORD sean correctos; si es necesario, restablezca el PASSWORD.
- **El usuario no puede completar la verificación 2FA**: Asegúrese de que la hora del sistema sea correcta, o restablezca el 2FA para el usuario y vuelva a vincular.
- **El menú de gestión de usuarios no es visible**: La cuenta actual puede no ser la cuenta designada de administrador del sistema.
- **Eliminación accidental de usuario**: La acción de eliminación no se puede deshacer directamente; la cuenta necesita ser recreada y los permisos relevantes reconfigurados.

> Credenciales generadas al crear, restablecer PASSWORDy restablecer 2FA deben guardarse inmediatamente y proporcionarse solo al titular de la cuenta.
