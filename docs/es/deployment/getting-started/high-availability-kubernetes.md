# Alta Disponibilidad Kubernetes Despliegue

[← ShimoDocs Suite Documentación de implementación](../README.md)

## 1. Escenarios Aplicables 

> [!TIP] 
> 
> K8s La implementación en clúster es adecuada para entornos de producción. En comparación con la implementación en una sola máquina, la implementación en clúster es más adecuada para operación a largo plazo, escalabilidad y escenarios de alta disponibilidad. 

- Para entornos de producción, se recomienda usar `3 master   N worker`. 
- Como mínimo, preparar 3 servidores, los 3 como maestros. Los trabajadores pueden reutilizar inicialmente los nodos maestros y luego aumentar los trabajadores según la escala. 

## 2. Preparativos Antes de la Implementación 

### 2.1 Preparar la Siguiente Información 

| Información | Ejemplo | Descripción | 
| --- | --- | --- | 
| Entorno de Red | En línea / Fuera de línea | Elegir En línea si se soporta acceso a red pública; elegir Fuera de línea para entornos de red interna o desconectados | 
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Seleccionar 1 máquina como nodo de instalación para iniciar la página web | 
| Negocio NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | Al menos 3 servidores | 
| Usuario para Ejecución | `root` | Los comandos de instalación deben ejecutarse con `root` | 
| Protocolo de Acceso | HTTP / HTTPS | HTTPS se recomienda para entornos de producción | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | La dirección para que los usuarios accedan ShimoDocs Suite |
| Directorio de Datos | `/data` | Se recomienda mantenerlo consistente en todos los nodos |
| Herramienta de Instalación | `mdp-installer-${Arch}` | Instalador proporcionado por ShimoDocs, `${Arch}` distingue diferentes arquitecturas de chip, su valor puede ser amd64 para arquitectura x86 o arm64 para arquitectura arm |
| Paquete de Instalación del Producto | ShimoDocs Suite paquete de instalación | Usar el nombre de archivo entregado real |
| Paquete de Imagen Offline | `*.tar.gz` | Solo necesario para instalación offline |
| Middleware Externo | Sí / No | Si hay middleware externo, preparar la dirección, puerto, cuenta, PASSWORD con anticipación |

### 2.2 Requisitos mínimos del servidor

| Ítem | Requisito |
| --- | --- |
| Número de Servidores | 3 o más |
| Roles Recomendados | `3 master   N worker` |
| CPU por Nodo | 16 Núcleos o más |
| Memoria por Nodo | 32 GB o más |
| Disco del Sistema | Root `/` partición de 100 GB o más |
| Disco de Datos | Montaje separado `/data`, espacio disponible 300 GB o más |
| Instalación Offline | Se recomienda reservar 100 GB adicionales o más en el disco de datos del nodo de instalación |

Nota:

- No particionar `/root`, `/var`, o `/tmp` por separado. 
- No poner datos en el disco del sistema; ponerlos todos en `/data`. 
- La hora en todos los nodos debe estar sincronizada. 
- Los nodos de instalación deben poder acceder a otros nodos vía SSH. 

Puede ejecutarse en cada servidor: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Confirmar que otros nodos pueden ser accesados desde el nodo de instalación: 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

Si el inicio de sesión falla, primero verificar SSH, PASSWORD, configuraciones de firewall o grupo de seguridad antes de continuar con la instalación.

## 3. Subir la herramienta de instalación y el paquete de instalación
> [!TIP]
>
> - Asegúrese de modificar los nombres de archivo en los comandos según la situación real. Por ejemplo, el nombre del paquete del instalador en un entorno de arquitectura x86 es mdp-installer-amd64.
> - Elija el método de carga adecuado según el escenario real. Este artículo utiliza la línea de comandos scp como ejemplo, pero también se pueden usar otras herramientas gráficas para cargar. SSH 

Ejecute el siguiente comando en su computadora local para transferir el instalador al nodo de instalación:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

La instalación sin conexión aún requiere la carga del paquete de imagen sin conexión: 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

Inicie sesión en el nodo de instalación: 

```bash
ssh root@<INSTALL_NODE_IP>
```

Otorgue permisos de ejecución al instalador:

```bash
chmod +x /root/mdp-installer-amd64
```

Abra la página web del instalador: 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

Acceso desde el navegador: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. Instalación a través de la página web

### 4.1 Cargar el paquete de instalación del producto

1. Abrir `http://<INSTALL_NODE_IP>:18080`.
2. Cargar el ShimoDocs Suite paquete de instalación.
3. Después de completar la carga, haga clic en `Continue`.

### 4.2 Configurar ACCESS_DOMAIN

Ingrese la ShimoDocs Suite dirección de acceso:

| Elemento de configuración | Cómo completar |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 Confirmar Configuración Básica

| Elemento de configuración | Cómo completar |
| --- | --- |
| NODE_IP | Rellenar maestro / trabajador NODE_IP una por una |
| SSH Puerto | Normalmente `22` |
| SSH PASSWORD | `root` usuario PASSWORD |
| Tipo de Nodo | `master`, `worker`, nodo de instalación |
| Directorio de Datos | `/data` |

Pasos de operación:

1. Agregar INSTALL_NODE_IP.
2. Agregar las direcciones IP de cada nodo maestro/trabajador.
3. Asignar roles de nodo a cada servidor.
4. Probar la conectividad desde el nodo de instalación hacia cada nodo.
5. Rellenar el catálogo de datos y el segmento de red de contenedores.

Puntos clave a confirmar durante la configuración:

- El protocolo de acceso y ACCESS_DOMAIN se rellenan correctamente.
- Pod CIDR y Servicio CIDR no entran en conflicto con la red existente, la red de oficina, VPN, o IDC segmentos de red.
- El directorio de datos utiliza `/data` o el directorio del disco de datos planificado real.
- El método de instalación en línea/fuera de línea es consistente con el entorno de red actual.
- La instalación fuera de línea requiere subir el paquete de imagen base fuera de línea y el paquete de imagen de la aplicación. Por defecto, es una instalación en línea, y es necesario asegurar que el clúster tenga acceso a la red pública.

### 4.4 Despliegue Inicial

Después de completar la configuración, haga clic en Inicializar Despliegue. La página mostrará un resumen de este despliegue; preste especial atención a:

- Versión del paquete del producto.
- Desplegar NODE_IP.
- SSH usuario y puerto.
- ACCESS_DOMAIN y protocolo.
- Directorio de datos.
- Modo de instalación en línea o fuera de línea.
- Selección de middleware.

Continuar después de confirmar que no hay errores.

### 4.5 Comprobar el Entorno del Sistema

El instalador comprobará automáticamente el entorno del servidor.

Continuar el despliegue después de que la inspección sea aprobada. Si ocurre algún fallo, siga las instrucciones en la página para solucionarlo y luego vuelva a inspeccionar. Las direcciones comunes de manejo incluyen:

- Espacio en disco insuficiente: limpiar espacio o ampliar el disco de datos.
- Puerto no disponible: liberar el puerto o ajustar el uso del puerto.
- SSH Conexión fallida: Verificar la cuenta, PASSWORD, clave privada, puerto y grupo de seguridad.
- Excepción de sincronización de tiempo: Configurar NTP o calibrar la hora del servidor.
- Faltan comandos básicos: Instale los comandos faltantes según la distribución del sistema.

### 4.6 Comenzar el Despliegue

Después de que se apruebe la verificación del entorno, haga clic en Iniciar Despliegue.

Puede ver los registros de ejecución de cada componente durante el proceso de despliegue. Durante la instalación, asegúrese de: 

- El proceso del instalador sigue en ejecución. 
- El navegador puede comunicarse con el nodo de instalación a través de la red. 
- El servidor no se reinicia. 
- No mueva ni elimine el paquete de instalación, el paquete de imagen fuera de línea o el directorio de datos. 

### 4.7 Esperar a que la Instalación se Complete

El proceso de instalación requiere un tiempo de espera, y el tiempo exacto depende del rendimiento del servidor, del entorno de red y de la velocidad de descarga de la imagen.

Cuando la página muestre que todas las tareas se han ejecutado con éxito y no hay componentes fallidos, indica que el despliegue se ha completado.

### 4.8 Confirmar el Resultado de la Instalación

Después de completar la instalación, el instalador mostrará la página de finalización del despliegue e información de acceso. Por favor, confirme primero que no hay tareas fallidas en la página antes de continuar para acceder al sistema de negocios y MDP Plataforma de Operaciones.

Visite la dirección de negocios: 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS fue configurado durante la instalación, por favor visite: 

```text
https://<ACCESS_DOMAIN>/
```

Después de iniciar sesión con la cuenta predeterminada o la cuenta de administrador, cambie la contraseña inicial PASSWORD inmediatamente.

Acceder a MDP Plataforma de Operaciones:

```text
http://<ACCESS_DOMAIN>/mdp/
```

Si necesitas modificar el MDP administrador PASSWORD, puedes ejecutar el siguiente comando en el nodo de implementación para modificar o restablecer el PASSWORD.
Por favor, reemplaza {password} con una nueva contraseña compleja y fuerte PASSWORD de acuerdo con los requisitos de seguridad reales.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 5. Aceptación Post-Instalación

### 5.1 Verificar K8s Estado del Nodo

Ejecuta en el nodo de implementación:

```bash
kubectl get node
```

El estado del nodo debería ser `Ready`. 

Continúa verificando el servicio: 

```bash
kubectl get pod -A
```

Los estados normales suelen ser: 

- `Running`: El servicio está en funcionamiento. 
- `Completed`: La tarea ha terminado de ejecutarse. 

Si te encuentras con estados como `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`, por favor primero revisa los registros del Pod correspondiente y manéjalos. 

### 5.2 Verificar la entrada de acceso 

Acceder a ShimoDocs Suite entrada a través del navegador: 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS está configurado, por favor visite: 

```text
https://<ACCESS_DOMAIN>/
```

Confirme que la página de inicio de sesión puede abrirse normalmente. 

### 5.3 Verificar el Backend de Gestión y la Licencia 

Confirme los siguientes elementos: 

- El backend de gestión es accesible. 
- Los administradores pueden iniciar sesión. 
- La página de Licencia puede abrirse. 
- Se puede ver la información de la máquina. 
- Se puede solicitar o actualizar la licencia según el proceso de autorización. 

### 5.4 Verificar Funciones de Negocio 

Después de iniciar sesión con una cuenta de prueba o una cuenta creada por un administrador, al menos verifique: 

- Puede crear documentos, hojas de cálculo, presentaciones. 
- Los documentos se pueden editar, guardar o actualizar, y el contenido sigue existiendo. 
- La edición colaborativa de múltiples usuarios está disponible. 
- La importación y exportación de archivos son normales. 
- Las funciones principales como búsqueda, espacio de equipo, contactos, etc., están disponibles. 

Después de iniciar sesión en la cuenta de prueba predeterminada por primera vez, por favor actualice su PASSWORD inmediatamente. 
Cuenta PASSWORD es la cuenta de despliegue y entrega PASSWORD! 

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 Detener el proceso del instalador

Después de que se complete y acepte el despliegue, se puede detener el servicio web del instalador
Detener la página web de instalación:
Comando para detener el instalador: 

```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

Si el instalador se inicia en segundo plano usando `nohup`, también puedes revisar los registros: 

```bash
tail -f /root/nohup.out
```

## 6. Manejo de problemas comunes

### 6.1 El navegador no puede abrir la página del instalador

Verifica lo siguiente:

- Si el proceso del instalador sigue en ejecución.
- Si el puerto `18080` está bloqueado por un firewall o grupo de seguridad.
- Si la IP accedida por el navegador es INSTALL_NODE_IP.

Puedes ejecutar en el servidor:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 Falló la verificación del entorno

Maneja cada elemento según los mensajes en la página. Después de manejarlo, regresa a la página del instalador y ejecuta nuevamente la verificación del entorno.

Verificaciones prioritarias:

- Si el CPU, la memoria y el disco cumplen con los requisitos.
- Si `/data` es un disco de datos independiente.
- Si la hora del servidor está sincronizada.
- Si el SSH El usuario tiene permisos de despliegue.

### 6.3 Fallo al descargar la imagen de instalación sin conexión

Verifica lo siguiente:

- Si el paquete de imagen sin conexión se ha subido al nodo de despliegue.
- Si el paquete de imagen sin conexión base y el paquete de imagen del producto están completos.
- Si la versión del paquete de imagen coincide con el paquete de instalación del producto.
- Si la dirección del repositorio de imágenes privado, la cuenta y PASSWORD se rellenan correctamente.

### 6.4 El Pod permanece en un estado anormal durante mucho tiempo

Primero, revisa el Pod anormal:

```bash
kubectl get pod -A
```

Revisa nuevamente los registros: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Maneja problemas con imágenes, configuraciones, recursos o dependencias según los registros.

## 7. Retención de materiales después de la instalación

Después del despliegue, se recomienda conservar los siguientes materiales para facilitar el mantenimiento, las actualizaciones y la resolución de problemas posteriores:

- INSTALL_NODE_IP, ACCESS_DOMAINy protocolo de acceso.
- Nombre y versión del archivo del instalador.
- Nombre y versión del archivo del paquete de instalación del producto.
- Nombre y versión del archivo del paquete de imagen sin conexión.
- Capturas de pantalla de la configuración de la clave de la página web.
- `kubectl get node` resultados de la verificación.
- `kubectl get pod -A` resultados de la verificación.
- Registros de autorización de licencia.
- Registros de aceptación de funciones comerciales.
- Problemas encontrados durante la implementación y sus resultados de manejo.
