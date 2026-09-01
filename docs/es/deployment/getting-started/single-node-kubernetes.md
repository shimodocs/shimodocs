# Despliegue de Nodo Único Kubernetes Despliegue

[← ShimoDocs Suite Documentación de implementación](../README.md)

## 1. Escenarios aplicables
- **K8s despliegue de un solo nodo**:
    - Apto para equipos pequeños y ligeros, uso a pequeña escala de menos de 200 personas, PoC, demostraciones, verificación de funciones y pruebas a corto plazo.
- Solo se necesita 1 servidor, y el servidor sirve simultáneamente como nodo de instalación, K8s nodo maestro y nodo trabajador de negocios.
- **Nota**
    - Para el lanzamiento oficial, la operación a largo plazo o la posterior escalabilidad de alta disponibilidad, se recomienda utilizar K8s despliegue en clúster.

## 2. Visión general del proceso de despliegue

| Paso | Qué hacer | Indicador de Finalización |
| --- | --- | --- |
| 1. Comprobar el entorno del sistema | Confirmar los recursos del servidor, disco, red, sincronización de tiempo y comandos básicos | El servidor cumple con los requisitos de despliegue |
| 2. Preparar los materiales de instalación | Obtener el instalador y el paquete de instalación del producto; un entorno sin conexión también requiere preparar un paquete de imagen sin conexión | El nombre del archivo coincide con la CPU arquitectura |
| 3. Subir los materiales de instalación | Subir el instalador y el paquete de instalación al nodo de despliegue | Los archivos se han colocado en el directorio especificado en el servidor |
| 4. Iniciar el instalador | Lanzar la `mdp-installer` página web | La página del instalador es accesible a través del navegador |
| 5. Instalación desde la página web | Seleccionar el paquete de distribución, configurar los nodos, completar la verificación del entorno e iniciar el despliegue | Todas las tareas de instalación se completaron con éxito |
| 6. Aceptación después de la instalación | Verificar clúster, servicios, inicio de sesión, licencia y funciones del negocio | Las funciones principales se pueden usar normalmente |

## 3. Preparación antes del despliegue

### 3.1 Preparar la información del servidor

| Información | Ejemplo | Descripción |
| --- | --- | --- |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | El despliegue de nodo único K8s utiliza solo 1 servidor |
| CPU Arquitectura | `amd64` / `arm64` | El instalador y el paquete de instalación deben coincidir con la arquitectura del servidor |
| Entorno de Red | En línea / Fuera de línea | Elegir en línea si se puede acceder a la red pública; elegir sin conexión para entornos internos o aislados |
| Usuario de ejecución | `root` o un usuario con `sudo` privilegios | El instalador necesita ejecutar tareas de despliegue mediante SSH |
| SSH Puerto | `22` | Si el SSH El puerto ha sido cambiado, rellene el puerto actual |
| Protocolo de Acceso | HTTP / HTTPS | HTTP puede usarse para entorno de pruebas; HTTPS se recomienda para producción o acceso externo |
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` o `<INSTALL_NODE_IP>` | La dirección de entrada para que los usuarios accedan ShimoDocs Suite |
| Directorio de Datos | `/data` | Se recomienda usar un montaje de disco de datos separado |

### 3.2 Preparar materiales de instalación

| Material | Nombre de archivo de ejemplo | Descripción |
| --- | --- | --- |
| Instalador | `mdp-installer-amd64` | Ejemplo para `amd64` arquitectura; reemplazar con el nombre de archivo real para otras arquitecturas |
| Paquete de instalación del producto | `co1.8.20260807.3639-drive-release..tar.gz` | Para implementación de nodo único K8s , elija el paquete de distribución cuyo nombre de archivo no incluya `k3s`; el nombre de archivo está sujeto a la entrega real |
| Paquete de imagen base sin conexión | `smbase_image-amd64.tar.gz` | Solo necesario para instalación offline |
| Paquete de imagen del producto sin conexión | `offline_app_image.tar.gz` | Solo se necesita para la instalación sin conexión, debe coincidir con la versión del paquete de instalación del producto |

Nota:

- Los nombres de archivos en los comandos deben reemplazarse con los nombres de archivos reales, tales como `mdp-installer-amd64`, `co1.8.<VERSION>-drive-release.tar.gz`.
- El paquete de instalación del producto, el paquete de imagen sin conexión y la arquitectura del servidor CPU deben ser consistentes.
- Antes de la instalación sin conexión, se recomienda preparar el paquete de imagen base y el paquete de imagen del producto de una vez para evitar agregar paquetes temporalmente durante la implementación.

### 3.3 Verificar recursos del servidor

| Ítem | Requisitos recomendados |
| --- | --- |
| Número de servidores | 1 |
| CPU | 16 núcleos o más |
| Memoria | 32 GB o más |
| Disco del sistema | Root `/` partición de 100 GB o más |
| Disco de Datos | Montado de manera independiente en `/data`, espacio disponible superior a 300 GB |
| Instalación Offline | Se recomienda reservar adicionalmente más de 100 GB en el disco de datos para paquetes de imagen y archivos de extracción temporal |

Ejecutar en el servidor: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Confirmar los siguientes resultados: 

- CPU, memoria y disco cumplen con las especificaciones de implementación. 
- `/data` ha sido montado en un disco de datos separado. 
- La sincronización de la hora del sistema es normal. 
- Se puede acceder al servidor mediante SSH inicio de sesión. 
- El entorno de instalación en línea puede acceder a la red pública; el entorno de instalación sin conexión tiene paquetes de imagen sin conexión listos. 

### 3.4 Comprobar Puertos 

| Puerto | Propósito | 
| --- | --- | 
| `22/TCP` | SSH inicio de sesión y ejecución de tareas de instalación | 
| `18080/TCP` | Página web del instalador | 
| `80/TCP` o `443/TCP` | ShimoDocs Suite entrada de acceso | 

Si el servidor tiene habilitado un firewall o grupo de seguridad, por favor abra los puertos anteriores con anticipación. 

## 4. Subir Herramientas y Paquetes de Instalación 

El siguiente ejemplo utiliza la `amd64` arquitectura. Para otras arquitecturas, por favor reemplace con los nombres de archivo reales. 

### 4.1 Subir el Instalador 

Ejecute en el equipo local: 

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

### 4.2 Subir Paquete de Imagen Sin Conexión

Este paso se puede omitir para la instalación en línea.

Para la instalación sin conexión, el paquete de imagen sin conexión necesita ser subido al nodo de implementación:

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

### 4.3 Iniciar sesión en el servidor

```bash
ssh root@<INSTALL_NODE_IP>
```

### 4.4 Agregar Permiso de Ejecución al Instalador

```bash
chmod +x /root/mdp-installer-amd64
```

### 4.5 Lanzar la Página Web del Instalador

Ejecute en el servidor:

```bash
cd /root
./mdp-installer-amd64 server
```

Si desea que el instalador se ejecute en segundo plano, puede usar: 

```bash
nohup /root/mdp-installer-amd64 server > /root/mdp-installer.log 2>&1 &
```

Acceso desde el navegador: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 5. Instalación a través de la Página Web

### 5.1 Selección del Paquete de Distribución

Después de ingresar a la página web del instalador, seleccione el paquete de distribución del producto que se desplegará esta vez.

Para K8s Para despliegue de un solo nodo, por favor elija el paquete de distribución cuyo nombre de archivo no contenga `k3s`, por ejemplo:

```text
co1.8.20260807.3639-drive-release.tar.gz
```

### 5.2 Configuración SSH Conexión

El instalador iniciará sesión en el nodo de implementación vía SSH y ejecutar las tareas de instalación. SSH la configuración admite dos métodos de autenticación:

- Autenticación por clave privada.
- PASSWORD Autenticación.

Se recomienda usar el `root` usuario o un usuario con `sudo` privilegios para realizar el despliegue. Después de completar la información, puede probar primero la conexión para asegurarse de que el instalador pueda iniciar sesión en el nodo de despliegue normalmente.

### 5.3 Confirmar Configuración Básica

Después de seleccionar el paquete de distribución, proceda al siguiente paso. Si no hay requisitos especiales, puede mantener la configuración predeterminada de la página; si el entorno de despliegue ya tiene un plan claro para nombres de dominio, certificados, segmentos de red o middleware, complete según el plan real.

Puntos clave a confirmar durante la configuración: 

- Asegúrese de que el protocolo de acceso y ACCESS_DOMAIN estén llenados correctamente. 
- Pod CIDR y Servicio CIDR no entran en conflicto con la red existente, la red de oficina, VPN, o IDC segmentos de red. 
- Use `/data` o el directorio de disco de datos realmente planificado para el directorio de datos. 
- El método de instalación en línea/fuera de línea es consistente con el entorno de red actual. 

### 5.4 Despliegue Inicial 

Después de completar la configuración, haga clic en Despliegue Inicial. La página mostrará una visión general de este despliegue. Por favor, concéntrese en verificar: 

- Versión del paquete del producto. 
- Despliegue NODE_IP. 
- SSH usuario y puerto. 
- ACCESS_DOMAIN y protocolo. 
- Directorio de datos. 
- Modo de instalación en línea o fuera de línea. 
- Selección de middleware. 

Continuar después de confirmar que todo es correcto. 

### 5.5 Verificar Entorno del Sistema

El instalador comprobará automáticamente el entorno del servidor.

Continuar con la implementación después de pasar la verificación. Si hay elementos con errores, por favor manéjelos según las indicaciones de la página y vuelva a verificar. Las instrucciones comunes de manejo incluyen: 

- Espacio en disco insuficiente: Libere espacio o amplíe el disco de datos. 
- Puerto no disponible: Libere el puerto o ajuste el uso del puerto. 
- SSH Conexión fallida: Verificar la cuenta, PASSWORD, clave privada, puerto y grupo de seguridad. 
- Sincronización de tiempo anormal: Configure NTP o calibre la hora del servidor. 
- Comandos básicos faltantes: Instale los comandos faltantes según la distribución del sistema. 

### 5.6 Iniciar implementación 

Después de que la verificación del entorno pase, haga clic para iniciar la implementación. 

Durante el proceso de implementación, puede ver los registros de ejecución de cada componente. Durante la instalación, asegúrese de: 

- El proceso del instalador sigue en ejecución. 
- Que el navegador pueda conectarse a la red del nodo de instalación. 
- No reiniciar el servidor. 
- No mueva ni elimine el paquete de instalación, el paquete de imagen fuera de línea o el directorio de datos. 

### 5.7 Esperar a que la instalación se complete

El proceso de instalación requiere esperar algún tiempo, y la duración específica depende del rendimiento del servidor, el entorno de red y la velocidad de descarga de la imagen.

Cuando la página muestre que todas las tareas se han ejecutado con éxito y no hay componentes fallidos, indica que el despliegue se ha completado.

### 5.8 Confirmando el resultado de la instalación

Después de completar la instalación, el instalador mostrará una página de finalización de implementación e información de entrada de acceso. Por favor confirme primero que no hay tareas fallidas en la página antes de continuar con el acceso al sistema de negocio y MDP Plataforma de Operaciones.

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

## 6. Aceptación posterior a la instalación

### 6.1 Verificar K8s Estado del Nodo

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

### 6.2 Verificar entrada de acceso 

Acceder a ShimoDocs Suite entrada a través del navegador: 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS está configurado, por favor visite: 

```text
https://<ACCESS_DOMAIN>/
```

Confirme que la página de inicio de sesión pueda abrirse normalmente.

### 6.3 Verificar el backend de gestión y la licencia

Confirme los siguientes elementos:

- Se puede acceder al backend de administración.
- El administrador puede iniciar sesión.
- La página de Licencia se puede abrir.
- Se puede ver la información de la máquina.
- La licencia se puede solicitar o actualizar según el proceso de autorización.

### 6.4 Verificar Funciones Comerciales

Después de iniciar sesión con una cuenta de prueba o una cuenta creada por un administrador, al menos verifique:

- Puede crear documentos, hojas de cálculo y presentaciones.
- El documento se puede editar y guardar, y el contenido sigue existiendo después de actualizar.
- La edición colaborativa de múltiples usuarios está disponible.
- La importación y exportación de archivos son normales.
- Las funciones principales como búsqueda, espacios de equipo y listas de contactos están disponibles.

Después de iniciar sesión con la cuenta de prueba predeterminada por primera vez, cambie PASSWORD inmediatamente.
La cuenta PASSWORD es la cuenta de entrega de implementación PASSWORD!
```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxx
```

### 6.5 Detener el proceso del instalador

Después de completar la implementación y aprobar la aceptación, se puede detener el servicio Web del instalador:
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

## 7. Solución de Problemas Común

### 7.1 El navegador no puede abrir la página del instalador

Verifica lo siguiente:

- Si el proceso del instalador sigue en ejecución.
- Si el puerto `18080` está bloqueado por un firewall o grupo de seguridad.
- Si la IP accedida por el navegador es INSTALL_NODE_IP.

Puede ejecutar lo siguiente en el servidor:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 7.2 Falló la verificación del entorno

Maneja cada elemento según los mensajes en la página. Después de manejarlo, regresa a la página del instalador y ejecuta nuevamente la verificación del entorno.

Verificaciones prioritarias:

- Si el CPU, la memoria y el disco cumplen con los requisitos.
- Si `/data` es un disco de datos independiente.
- Si la hora del servidor está sincronizada.
- Si el SSH El usuario tiene permisos de despliegue.

### 7.3 Falló la extracción de la imagen de instalación sin conexión

Verifica lo siguiente:

- Si el paquete de imagen sin conexión se ha subido al nodo de despliegue.
- Si el paquete de imagen sin conexión base y el paquete de imagen del producto están completos.
- Si la versión del paquete de imagen coincide con el paquete de instalación del producto.
- Si la dirección del repositorio de imágenes privado, la cuenta y PASSWORD se rellenan correctamente.

### 7.4 El pod permanece en un estado anormal durante mucho tiempo

Primero, revisa el Pod anormal:

```bash
kubectl get pod -A
```

Revisa nuevamente los registros: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Maneja problemas con imágenes, configuraciones, recursos o dependencias según los registros.

## 8. Conservar Materiales Después de la Instalación

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
