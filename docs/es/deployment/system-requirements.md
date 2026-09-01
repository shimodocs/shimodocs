# Requisitos del Sistema

[← ShimoDocs Suite Documentación de implementación](README.md)

## 1. Preparar Recursos Según el Escenario

| Escenario de Uso | Despliegue Recomendado | Preparación de Recursos |
| --- | --- | --- |
| Equipo pequeño y ligero, PoC, demostración, verificación de funcionalidades | Despliegue de un solo servidor | 1 servidor |
| Lanzamiento oficial, operación a largo plazo, que requiere alta disponibilidad o escalabilidad futura | Cluster de alta disponibilidad | 3 o más servidores |

- El despliegue de un solo servidor es adecuado para verificación rápida y uso a pequeña escala.
- El despliegue de clúster es adecuado para el lanzamiento oficial, la operación a largo plazo y la escalabilidad futura.

## 2. Requisitos del sistema operativo

| Sistema Operativo | Versiones compatibles | Arquitectura compatible |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

Ejecute en cada servidor:

```bash
cat /etc/os-release
uname -m
```

Resultados de la confirmación: 

- El sistema operativo es Ubuntu 22.04 o Ubuntu 24.04. 
- CPU La arquitectura es x86. 
- La cuenta de instalación es `root`, o tiene privilegios equivalentes de administración del sistema. 

Nota: Razones por las cuales ya no se admite el sistema CentOS 
- CentOS Linux 7 y 8 han llegado al final de su ciclo de vida, CentOS oficialmente ya no proporciona CentOS 9 y versiones posteriores, y ya no recibe nuevas actualizaciones de seguridad, correcciones de vulnerabilidades ni parches de problemas. 
- Los componentes básicos del sistema no pueden recibir parches de seguridad a largo plazo, lo que podría dejar vulnerabilidades expuestas pero irremediables, que no cumplen con los requisitos de seguridad del entorno de producción. 
- El kernel, glibc, OpenSSL y otros componentes básicos en CentOS 7/8 son relativamente antiguos y no pueden cumplir con los requisitos de nuevas Kubernetes librerías de ejecución y dependencias. 
- CentOS Stream tiene un posicionamiento de versiones y un mecanismo de actualización diferente en comparación con el CentOS Linux tradicional, y los entornos de CentOS Stream que no han pasado por una verificación especial de compatibilidad tampoco están incluidos dentro del soporte oficial. 


## 3. Requisitos de Configuración del Servidor 

### 3.1 Implementación de nodo único 

- Adecuado para equipos pequeños y ligeros, de menos de 200 personas. 
- PoC, los escenarios de demostración y verificación funcional pueden prepararse de acuerdo con los recursos de un solo nodo. 

| Proyecto | Requisito | 
| --- | --- | 
| Número de servidores | 1 | 
| CPU | 16 núcleos o más |
| Memoria | 32 GB o más |
| Disco del Sistema | Root `/` partición de 100 GB o más |
| Disco de Datos | Montado de forma independiente `/data`, espacio disponible 300 GB o más, compatible con ampliación |

### 3.2 Despliegue del Clúster

Para escenarios que requieren lanzamiento oficial, operación a largo plazo, alta disponibilidad o expansión futura, prepare los recursos de acuerdo con los requisitos del clúster.

| Ítem | Requisito |
| --- | --- |
| Número de Servidores | 3 o más |
| Roles Recomendados | `3 master   N worker` |
| CPU por Nodo | 16 núcleos o más |
| Memoria por Nodo | 32 GB o más |
| Disco del sistema por nodo | Root `/` partición de 100 GB o más |
| Disco de datos por nodo | Montado de forma independiente `/data`, espacio disponible 300 GB o más, compatible con ampliación |

Notas de partición:

- Mantenga al menos 100 GB para la raíz `/` partición.
- Se recomienda colocar `/root`, `/var`, `/tmp` bajo la partición raíz para una gestión unificada.
- Use un disco de datos independiente para el directorio de datos, montado en `/data`.

## 4. Comandos de autocomprobación del servidor

Ejecutar en cada servidor: 

```bash
# ============================================
# 1. View CPU architecture and core information
#    - Architecture type (x86_64/aarch64)
# ============================================
lscpu

# ============================================
# 2. Display memory and swap usage in GiB
# ============================================
free -g

# ============================================
# 3. File System Disk Space Usage
# ============================================
df -h

# ============================================
# 4. Find the executable file path
# ============================================
which iptables gzip tar

# ============================================
# 5. Display system time, time zone, and NTP synchronization status
#    Distributed clusters must have strict time synchronization, otherwise it will affect authentication and log sequencing.
# ============================================
timedatectl status
```

Lista de verificación de comparación:

| Elemento de inspección | Condición de aprobación |
| --- | --- |
| CPU | 16 núcleos o más |
| Memoria | 32 GB o más |
| Disco del Sistema | Root `/` espacio disponible en la partición 100 GB o más |
| Disco de Datos | `/data` montado, espacio disponible 300 GB o más |
| Comandos básicos | `iptables`, `gzip`, `tar` puede encontrarse |
| Sincronización de tiempo | La sincronización de la hora del sistema es normal |

## 5. Requisitos del navegador

| Navegador | Requisitos de Versión |
| --- | --- |
| Chrome | 86 o superior |
| Safari | 11 o superior |
| Firefox | 102 o superior |
| Edge | 84 o superior |

Se recomienda priorizar el uso de la versión más reciente de Chrome o Edge para acceder al instalador y ShimoDocs Suite.

## 6. Requisitos de Middleware

| Componente | Requisitos de Versión |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 a 3.5 |
| Almacenamiento de Objetos | Compatible con S3 protocolo<br>y asegúrese de que su dirección de Endpoint pueda ser directamente accesible por los navegadores del cliente desde la red pública (ya que ShimoDocs la carga de recursos estáticos de la aplicación y las operaciones de lectura/escritura de documentos deben completarse mediante conexiones directas del navegador al almacenamiento de objetos). |

El almacenamiento de objetos puede ser Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, AWS S3. Para implementación local, usar MinIO puede ser considerado.

Si se utiliza middleware integrado en el instalador, continúe con las opciones predeterminadas en la página de instalación. 
Si se utiliza middleware externo existente, prepare la dirección, puerto, cuenta, PASSWORD, DATABASE_NAME o nombre del Bucket antes de la instalación.

## 7. Requisitos de Puertos

Antes del despliegue, asegúrese de que el servidor, grupo de seguridad, firewall, balanceador de carga y políticas de red hayan permitido los siguientes puertos.

| Puerto | Objetivo | Propósito |
| --- | --- | --- |
| `18080/TCP` | Interfaz web del instalador | Acceder a la página de instalación |
| `80/TCP` o `443/TCP` | ShimoDocsSERVICE_DOMAIN | Entrada de acceso de usuario |
| `22/TCP` | Todos los nodos de despliegue | SSH inicio de sesión y distribución de tareas de instalación |
| `3306/TCP` | MySQL | Conexión a la base de datos |
| `6379/TCP` | Redis | Conexión a la caché |
| `27017/TCP` | MongoDB | Conexión a la base de datos de documentos |
| `9092/TCP` | Kafka | Conexión a la cola de mensajes |
| `9200/TCP` | Elasticsearch | Conexión al servicio de búsqueda |
| Por puerto de servicio | S3 / OBS / OSS / COS / MinIO | Conexión al almacenamiento de objetos |

## 8. Requisitos de E/S de Disco

Se recomienda usar SSDs para los discos de datos. El rendimiento del disco debe cumplir con los siguientes estándares:

| Ítem | Requisito |
| --- | --- |
| Lectura/escritura mixta IOPS | Más de 5000 |
| Rendimiento de lectura/escritura secuencial | Más de 150 MB/s |
| Latencia promedio | Alrededor de 5 ms o menor |

Después de la instalación `fio`, se pueden realizar pruebas en `/data`.

### 8.1 Prueba de Lectura/Escritura Mixta

```bash
fio --name=randrw-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=randrw \
  --rwmixread=70 \
  --bs=4k \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=4 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Preste atención al IOPS en los resultados; la lectura/escritura mixta IOPS debe alcanzar más de 5000 para continuar. 

### 8.2 Prueba de Lectura Secuencial

```bash
fio --name=seqread-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=read \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

### 8.3 Prueba de Escritura Secuencial

```bash
fio --name=seqwrite-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=write \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

El rendimiento de lectura y escritura secuencial que alcance más de 150 MB/s puede continuar. 

Los archivos de prueba pueden eliminarse después de la prueba: 

```bash
rm -f /data/testfile
```

## 9. Requisitos de Ancho de Banda de Red Pública

Estime el ancho de banda para escenarios de acceso a la red pública según el número de usuarios:

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

Ejemplo:

| Número de Usuarios | Ancho de banda de red pública recomendado |
| --- | --- |
| 100 usuarios | Más de 25 Mbps |
| 200 usuarios | Más de 50 Mbps |
| 500 usuarios | Más de 125 Mbps |

Para escenarios de acceso a intranet, también se recomienda evaluar el ancho de banda de salida, entrada y balanceo de carga usando los mismos criterios.

## 10. Recomendaciones de versión del navegador para la plataforma de Instalador y Operaciones

Se recomienda usar Google Chrome versión 111 o superior, preferiblemente la última versión estable.

## 11. Lista de verificación previa a la implementación

Antes de comenzar la instalación, confirme cada ítem:

- La versión del sistema operativo cumple con los requisitos.
- CPU, memoria, disco del sistema y disco de datos cumplen con los requisitos.
- `/data` está montado en un disco de datos separado.
- `iptables`, `gzip`, y `tar` están instalados.
- La sincronización de la hora del sistema es normal.
- Se ha determinado el método de instalación en línea o fuera de línea.
- Puerto del instalador `18080` es accesible.
- Puertos de acceso empresarial `80` o `443` están abiertos.
- Si se utiliza middleware externo, la información de conexión está completamente preparada.
- El almacenamiento de objetos es compatible con el S3 protocolo, y los permisos del bucket y la cuenta están listos. 
- Las pruebas de IO del disco de datos cumplen con los requisitos. 
- El ancho de banda de la red pública o interna cumple con el número esperado de usuarios.
