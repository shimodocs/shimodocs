# Resumen de la Plataforma de Operaciones

[← ShimoDocs Suite Documentación de implementación](../README.md)

## Descripción de la Función

- **ShimoDocs Suite**: Se utiliza para gestionar autorizaciones, inquilinos, usuarios, marca y configuraciones de IA relacionadas con ShimoDocs Suite.
- **Servicios del sistema**: Se utiliza para tareas generales de operación y mantenimiento, como configuración global, gestión de clústeres, visualización de registros, inspección de funciones, consulta de problemas, reparación de documentos y **actualizaciones del sistema**.

> **Nota**: La funcionalidad específica que se muestra depende de la versión de implementación actual y de las funciones habilitadas.

## Inicio de sesión en la Plataforma de Operaciones

Acceda a la siguiente dirección en su navegador:
> **Requisitos del navegador**: Utilice Google Chrome versión 111 o superior para acceder a la Plataforma de Operaciones. Se recomienda actualizar primero a la última versión estable.

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

Ingrese la cuenta de administrador y PASSWORD, luego haga clic en "Iniciar sesión."

## Conociendo la página de inicio de la Plataforma de Operaciones

Después de iniciar sesión, puede acceder a las funciones de gestión correspondientes a través del menú en el lado izquierdo de la página. El menú que se muestra depende de los productos y versiones que estén implementados y autorizados en el entorno actual.

## Restableciendo el administrador PASSWORD Cuando se olvida

Si se olvida el administrador de la Plataforma de Operaciones PASSWORD puede iniciar sesión en el nodo de implementación y ejecutar el siguiente comando para restablecerlo.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

El ejemplo anterior restablece el PASSWORD a `Aa1234567.`. En la operación real, reemplace el ejemplo PASSWORD al final del comando con un nuevo PASSWORD que cumpla con los requisitos de seguridad.

Después de que se complete el restablecimiento, vuelva a la página de inicio de sesión, inicie sesión con el nuevo PASSWORDy confirme que se puede acceder al menú normalmente.
