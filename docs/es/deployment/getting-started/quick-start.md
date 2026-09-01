# Inicio Rápido

[← ShimoDocs Suite Documentación de implementación](../README.md)

> [!TIP]
>
> Este artículo introduce cómo usar el `mdp-installer` para desplegar rápidamente un entorno nuevo de ShimoDocs Suite.
>
> Este artículo está escrito para el **Todos en uno de nodo único, instalación en línea** escenario, adecuado para la instalación por primera vez, experiencia del producto, verificación de la funcionalidad y práctica del proceso de implementación. Después de completar este artículo, podrá obtener la ShimoDocs Suite dirección de acceso empresarial y la MDP dirección de la Plataforma de Operaciones.

> Las direcciones IP, nombres de paquetes de instalación, VERSIONy directorios en la página son solo a modo de ejemplo. Durante la implementación real, consulte el entorno actual y los materiales entregados.

## 1. Resumen del Proceso de Implementación

El proceso completo se puede dividir en los siguientes 7 pasos:

| Paso | Acción a Completar | Indicador de Finalización |
| --- | --- | --- |
| 1. Preparar Servidores y Materiales de Instalación | Asegúrese de que los servidores, el instalador y el ShimoDocs Suite paquete de distribución estén disponibles | Poder iniciar sesión en el servidor y localizar los archivos de instalación |
| 2. Iniciar el Instalador | Ejecutar `mdp-installer` en el nodo de instalación | La terminal muestra la dirección de acceso del instalador |
| 3. Subir el Paquete de Distribución | Seleccione el ShimoDocs Suite paquete de distribución en el navegador | La página indica que el paquete de distribución ha pasado la verificación |
| 4. Configurar el Entorno de Despliegue | Complete el dominio o IP, el modo de despliegue, la información de inicio de sesión del nodo y los directorios de datos | Los nodos se verifican correctamente y se puede acceder a la vista general del despliegue |
| 5. Verificación del Entorno | Espere a que el instalador revise el servidor y el entorno de despliegue | No hay fallos que bloqueen la instalación |
| 6. Iniciar la Instalación | Confirme los resultados de la verificación y ejecute el despliegue | La página muestra que la instalación está completa |
| 7. Guardar la Información de Entrega | Guarde la dirección de acceso y complete la verificación de inicio de sesión, servicio y funciones | Páginas de negocios y MDP Se puede acceder a la Plataforma de Operaciones |

## 2. Preparación previa a la implementación

### 1. Preparar un nodo de instalación

El nodo de instalación se utiliza para ejecutar el instalador y sirve como el servidor objetivo para esta implementación All-in-One de nodo único.

Antes de comenzar, confirme por favor:

- Se ha preparado un servidor utilizable y el SERVER_Se ha obtenido la dirección IP.
- Se puede acceder al servidor mediante SSH.
- El SSH usuario es `root`, o tiene los permisos necesarios para realizar tareas de despliegue.
- El servidor CPU la arquitectura coincide con el instalador y el paquete de distribución, por ejemplo, ambos son `x86_64`.
- El servidor cumple con las especificaciones de despliegue actuales; se recomienda usar la instalación mínima de Ubuntu 24.04 LTS.
- La partición raíz y el espacio de datos cumplen con los requisitos de despliegue actuales, y se ha determinado el directorio de datos.
- La hora del servidor y la zona horaria son correctas, y la sincronización de tiempo es normal.
- La computadora con el navegador puede acceder al puerto `18080/TCP` del nodo de instalación.
- El servidor puede acceder a Internet para descargar paquetes de despliegue y recursos de imagen en línea.
- Si el acceso al negocio utiliza nombres de dominio, la resolución de nombres de dominio se ha completado previamente (opcional).

Los requisitos mínimos del servidor son los siguientes:

| Sistema Operativo | Arquitectura | CPU | Memoria | Disco |
| --- | --- | --- | --- | --- |
| Ubuntu 24.04 LTS | `x86_64` | 16 Núcleos | 32 GB | 100 GB SSD |

Además, confirme, por favor:

- No particionar `/root`, `/var`, `/tmp` por separado.
- Antes de la instalación, no despliegue componentes adicionales como Docker o Kubernetes en el servidor que puedan afectar las verificaciones del instalador.
- Puerto `22/TCP` se utiliza para SSH, `18080/TCP` se utiliza para la página web del instalador, `80/TCP` y `443/TCP` se utilizan para el acceso al negocio.

> Antes del despliegue oficial, se recomienda confirmar las especificaciones del servidor según la concurrencia real, el tamaño de archivo y los requisitos de disponibilidad; el proceso de nodo único en este documento es adecuado para un despliegue rápido y verificación; para operación a largo plazo o alta disponibilidad, utilice el esquema de despliegue de clúster correspondiente.

### 2. Preparar Materiales de Instalación

#### Obtener el Instalador

Suba el instalador proporcionado por ShimoDocs al directorio `/root/` del nodo de instalación. Puede elegir cualquiera de los siguientes métodos:

- **Método 1: Subir vía SSH herramienta**. Suba `mdp-installer-amd64` al directorio `/root/` directorio del nodo de instalación.

#### Obtener el paquete de distribución

Preparar el ShimoDocs Suite paquete de distribución para esta implementación. El paquete de distribución se carga en la página web de instalación, y el nombre del archivo y la versión deben seguir la entrega real.

Ejemplo de nombre de archivo: `co1.8.20260711.3286-drive-release.tar.gz`

La lista de materiales es la siguiente:

| Archivo | Descripción |
| --- | --- |
| `mdp-installer` Instalador | Elija el archivo correspondiente según la arquitectura del servidor, por ejemplo, `mdp-installer-amd64`. |
| ShimoDocs Suite Paquete de distribución | El nombre del archivo y la versión deben seguir la entrega real, por ejemplo, `co1.8.20260711.3286-drive-release.tar.gz`. |

Se recomienda colocar el instalador y los materiales de instalación relacionados en el mismo directorio de trabajo para facilitar la posterior recuperación y almacenamiento. Antes de usar el paquete de distribución, asegúrese de que los archivos estén completos y que no estén dañados por la herramienta de transferencia.

## 3. Iniciar el Instalador

### 1. Iniciar sesión en el Nodo de Instalación

Inicie sesión en el nodo de instalación mediante SSH y navegue hasta el directorio donde se encuentra el instalador. Por ejemplo:

```bash
ssh root@<INSTALL_NODE_IP>
cd /root
```

### 2. Agregar permiso de ejecución

Si el instalador aún no tiene permiso de ejecución, ejecute:

```bash
chmod +x ./mdp-installer-amd64
```
El nombre del archivo en el comando debe reemplazarse con el nombre real del instalador. 

### 3. Lanzar la página de instalación web 

Ejecute: 

```bash
./mdp-installer-amd64 server
```

Si necesita que el instalador continúe ejecutándose después de que la terminal actual cierre, puede usar: 

```bash
nohup ./mdp-installer-amd64 server > nohup.out 2>&1 &
```

Después de iniciarse correctamente, la terminal mostrará dos direcciones: 

- `Local`: Para uso únicamente del propio nodo de instalación. 
- `Network`: Accesible por otros equipos en la misma red. 

Si se inició en modo de fondo, puede ejecutar el siguiente comando para ver la salida del instalador: 

```bash
cat nohup.out
```

Abrir el `Network` dirección mostrada en el terminal en un navegador, por ejemplo:

```text
http://<INSTALL_NODE_IP>:18080/
```

> Durante la instalación, mantenga el proceso del instalador en ejecución. No cierre el proceso del instalador ni detenga el servicio actual.

## 4. Subir ShimoDocs Suite Paquete de lanzamiento

### 1. Seleccione el Paquete de Lanzamiento

Después de ingresar a la página de instalación:

1. Haga clic **Iniciar Despliegue** o en la entrada de selección del paquete de lanzamiento en la página.
2. Seleccione el ShimoDocs Suite `.tar.gz` paquete de lanzamiento que se utilizará para este despliegue.
3. Espere a que la carga y la verificación del archivo se completen.

### 2. Confirmar Resultados de Verificación

Después de que la verificación pase, la página mostrará el nombre del paquete de lanzamiento y la entrada de configuración de despliegue.

Por favor, confirme que la siguiente información es correcta:

- El nombre del paquete es consistente con la versión entregada esta vez.
- El paquete de lanzamiento pertenece al ShimoDocs Suite producto.
- La página no indicó corrupción de archivos, errores de formato o incompatibilidad de esquema.

Después de confirmar, haga clic en **Continuar** para ir a la configuración del despliegue.

Si la verificación falla, vuelva a confirmar si el paquete de distribución está completo, si el tipo de archivo es correcto y si el paquete de distribución coincide con la CPU arquitectura del servidor.

## 5. Configuración del Entorno de Despliegue

### 1. Confirmar la dirección de red

Verifique el nombre de host o la dirección IP identificada en la página. Esta dirección debe ser la dirección del nodo de instalación a la que el usuario puede acceder normalmente.

No ingrese `127.0.0.1`, y no utilice direcciones temporales que solo puedan ser accesadas por la computadora actual. Al acceder a través de un nombre de dominio, asegúrese de que el nombre de dominio se haya resuelto a la entrada de servicio correcta.

### 2. Seleccionar Modo Nodo Único Todo en Uno

Seleccionar **Nodo Único Todo en Uno** en el modo de implementación o entorno objetivo (el nombre real mostrado en la página está sujeto a la versión actual).

En este modo, el nodo de instalación asume simultáneamente los roles de control y de negocio para esta implementación, lo que lo hace adecuado para entornos ligeros para la experiencia del producto, validación de funciones y planificación de un solo nodo.

### 3. Configurar Nodo SSH

El instalador se conecta al nodo objetivo a través de SSH y ejecuta tareas de implementación. Por favor complete:

- NODE_Dirección IP.
- SSH Usuario, usualmente `root`.
- SSH Puerto, usualmente `22` por defecto.
- PASSWORD o información de autenticación con clave privada.

Después de completar, haga clic en **Verificar** para confirmar que la SSH conexión es exitosa.

> SSH Las credenciales solo deben ser usadas y almacenadas en un entorno controlado. No escriba PASSWORD o claves privadas en documentos públicos, capturas de pantalla o registros de chat.

### 4. Configurar Directorio de Datos y Otras Configuraciones

Complete o confirme las siguientes configuraciones según los indicaciones de la página:

| Elemento de configuración | Descripción |
| --- | --- |
| ACCESS_DOMAIN / IP | Dirección para que los usuarios accedan ShimoDocs Suite; al usar una IP, ingrese la dirección real accesible. |
| Modo de Implementación | Seleccione el modo de nodo único todo en uno. |
| Directorio de Datos del Nodo | Se utiliza para almacenar datos de la implementación. Por favor asegúrese de que el disco tenga suficiente espacio y permisos de lectura/escritura. |
| Repositorio Offline | Esta guía es para la instalación en línea; mantenga el valor predeterminado en la página. |
| Middleware de terceros | Esta guía utiliza la implementación predeterminada; confirme de acuerdo con los requisitos de entrega actuales si se necesita middleware externo. |

Si no hay requisitos de configuración especiales, puede mantener los valores predeterminados para el repositorio fuera de línea y el middleware de terceros. Después de verificar, haga clic **Inicializar Implementación** en la parte inferior de la página.

## 6. Confirmar Resumen de Implementación

El resumen de implementación se utiliza para verificar la configuración de instalación antes de la verificación formal.

Preste especial atención a lo siguiente:

- Asegúrese de que la versión del paquete de lanzamiento y el nombre del producto sean correctos.
- ACCESS_DOMAIN o la IP es correcta y no es `127.0.0.1`.
- El modo de implementación es nodo único Todo en Uno.
- NODE_IP, SSH usuario y puerto son correctos.
- El directorio de datos es correcto y el espacio en disco es suficiente.
- La configuración del repositorio fuera de línea y del middleware de terceros cumple con el entorno actual.

Después de confirmar que no hay errores, haga clic **Continuar** para proceder a la comprobación del entorno.

## 7. Realizar comprobación del entorno

El instalador verificará los nodos y el entorno de implementación. El proceso de verificación puede tardar unos minutos, por favor mantenga la página abierta.

### 1. Ver resumen de nodos

El resumen de nodos muestra el progreso de las verificaciones como SSH conectividad, sistema y rendimiento, almacenamiento y disco, red y entorno de implementación.

Para ver los resultados detallados de una verificación específica, haga clic en el elemento de verificación correspondiente o en la entrada de detalle.

### 2. Ver resultados detallados de la verificación

Los resultados detallados típicamente incluyen:

- SSH permisos de usuario para conectividad y ejecución.
- Sistema operativo, CPU Arquitectura y número de núcleos.
- Capacidad de memoria, espacio en disco y permisos de directorio.
- Zona horaria y estado de sincronización de tiempo.
- Red, recursos de imagen y conectividad con servicios externos.
- Residuos ambientales en el servidor que pueden afectar la implementación.

### 3. Comprender el estado de la verificación

| Estado | Significado | Próxima acción |
| --- | --- | --- |
| Éxito | El elemento de verificación actual cumple con los requisitos de implementación | Seguir esperando a que se completen otros elementos |
| Advertencia | No bloqueará directamente el despliegue, pero necesita confirmación si se alinea con el plan actual | Abrir detalles y continuar después de confirmar el impacto |
| Fallo | El problema actual puede afectar la instalación o el funcionamiento del producto | Solucione el problema primero, luego vuelva a escanear |
| En progreso | El instalador está realizando la verificación | Espere a que finalice la verificación, no realice acciones repetidas |

Si un elemento permanece en 'En progreso' durante mucho tiempo, puede esperar primero a que se complete la verificación actual del disco o remota antes de decidir si volver a escanear.

### 4. Manejo de advertencias y fallos

Si la página muestra una advertencia:

1. Abra la descripción detallada del elemento de verificación correspondiente.
2. Confirme si la advertencia se alinea con el plan de despliegue actual.
3. Si tiene dudas, guarde la página y los registros del instalador, luego contacte al personal de implementación u operaciones para obtener confirmación.

Si la página no se muestra: 

1. Siga las indicaciones para solucionar SSHproblemas de permisos, recursos, disco, red o middleware. 
2. Haga clic **Volver a escanear**. 
3. Confirme que los elementos fallidos hayan desaparecido. 

Después de asegurarse de que no haya fallos que bloqueen la implementación y que se hayan confirmado todas las advertencias, haga clic en **Continuar**. 

## 8. Iniciar Instalación 

### 1. Confirmar el Plan de Instalación 

La página mostrará el plan de instalación y las tareas a ejecutar. Después de confirmar que son correctas, haga clic en **Iniciar Despliegue**. 

Puede aparecer un mensaje de "Confirmar para Iniciar Instalación" en la página. Una vez iniciada, la tarea de instalación se ejecutará según lo programado; si necesita ajustar la configuración, haga clic en **Cancelar** para volver al paso anterior.

### 2. Comprobar el Progreso de la Implementación

Después de iniciar la implementación, la página mostrará el estado actual de la tarea, los registros en tiempo real y el tiempo de ejecución. La implementación en un solo nodo generalmente toma alrededor de 10 minutos, y el tiempo real se verá afectado por el rendimiento del servidor y el ancho de banda de la red.

Durante el proceso de instalación, tenga en cuenta: 

- No cierre el proceso del instalador. 
- No reinicie los nodos de instalación. 
- No actualice, retroceda ni reenvíe la tarea de instalación. 
- Si la tarea falla, primero consulte el primer error en el registro de la tarea correspondiente y luego actúe según la indicación. 

Cuando la página muestre **Instalación Completada** o ingrese a la **página de Entrega de Implementación** , indica que la tarea de instalación ha finalizado. 

## 9. Guardar Información de Entrega 

La página de finalización de la instalación mostrará la información de acceso y la verificación de entrada para esta implementación. Por favor, complete inmediatamente las siguientes acciones: 

1. Ejecute la verificación del servicio posterior a la instalación y confirme los resultados de la verificación. 
2. Utilizando la información de acceso en la página de entrega de la implementación, abra la ShimoDocs Suite página de negocios y complete la verificación de inicio de sesión. 
3. Registre la ShimoDocs Suite dirección de acceso empresarial y la MDP dirección de la Plataforma de Operaciones. 
4. Guarde la cuenta inicial y temporal PASSWORD, y cambie inmediatamente la inicial PASSWORD después del primer inicio de sesión. 
5. Verifique los nodos del clúster y el estado de la aplicación en la MDP Plataforma de Operaciones. 

> La información de entrega incluye direcciones de acceso y credenciales iniciales. No tome capturas de pantalla ni las distribuya, no las cargue en bases de conocimiento públicas y no las envíe a través de canales no controlados. 

## 10. Verificar Resultados de la Implementación 

Después de completar la instalación, se recomienda realizar la aceptación en el siguiente orden: 

### 1. Verificar Servicios Posteriores a la Instalación 

Realice verificaciones posteriores a la instalación en la página de finalización para confirmar que los casos de prueba del servicio pasan o que los resultados cumplen con las expectativas del entorno actual. 

Si la verificación falla o pasa parcialmente, puede volver a enviar una tarea de inspección en la MDP Plataforma de Operaciones. 

### 2. Verificar MDP Plataforma de Operaciones

Inicie sesión en la MDP Plataforma de Operaciones, vaya a **Servicios del Sistema → Gestión de Clúster**, y confirme que los nodos del clúster y el estado de ejecución de la aplicación son normales.

### 3. Verificar ShimoDocs Suite Funciones

Inicie sesión en la ShimoDocs Suite página frontend y verificar al menos las siguientes funciones:

- Crear un archivo o suite de prueba.
- Editar contenido y guardar.
- Exportar archivos.
- Importar archivos.

Después de que todas las comprobaciones anteriores se hayan completado con éxito, indica que este despliegue rápido está completo. Si se requiere operación a largo plazo, escalabilidad o alta disponibilidad en el futuro, cambie al plan de despliegue correspondiente según la escala real, y complete la configuración de licencia y negocio.

## 11. Preguntas comunes

### 1. El navegador no puede abrir la página de instalación

Verificar en orden:

- Si el proceso del instalador sigue en ejecución.
- Si la dirección de acceso utiliza la IP real del nodo de instalación o un nombre de dominio resoluble.
- Si el puerto `18080/TCP` ha sido abierto.
- Si la red entre la computadora con el navegador y el nodo de instalación está conectada.

### 2. Falló la verificación del paquete de distribución

Verificar:

- Si el archivo subido es un `.tar.gz` paquete de versión completo.
- Si el nombre del archivo y el tipo de producto son consistentes con esta entrega.
- Si el paquete de versión coincide con el servidor CPU arquitectura del servidor.
- Si el archivo está dañado durante la subida o transferencia.

### 3. SSH Autenticación fallida

Verificar:

- Si el NODE_IP y SSH puerto son correctos.
- Si el SSH usuario, PASSWORD, o llave privada es correcta.
- ¿El SSH usuario tiene los permisos requeridos para el despliegue?
- Si el firewall o grupo de seguridad permite SSH conexiones.

### 4. Advertencias en la verificación del entorno

Las advertencias no impedirán directamente el despliegue, pero necesita abrir los detalles para confirmar el impacto. Si involucra el rendimiento del disco, la sincronización de tiempo, residuos de configuración o servicios externos, primero confirme si se alinea con el plan de despliegue actual antes de decidir si continuar.

### 5. Fallos en la Verificación del Entorno

Los elementos con fallos deben ser corregidos primero. No omita la verificación y comience la instalación directamente. Después de corregirlos, haga clic **Volver a escanear** para confirmar que los elementos con fallos han pasado.

### 6. Fallo en la Tarea de Instalación

1. Abra el registro de ejecución de la tarea fallida.
2. Encuentre el primer mensaje de error que apareció.
3. Guarde el registro del instalador, el nombre de la tarea fallida y el momento de ocurrencia.
4. Después de abordar los correspondientes problemas de red, disco, imagen, middleware o Kubernetes otros, continúe según el método de recuperación real.
