# Referencia de Métricas de Monitoreo

[← ShimoDocs Suite Documentación de implementación](../README.md)

Este documento organiza métricas de uso común en el sistema de monitoreo, cubriendo nodos, contenedores containerd, Kubernetes clústeres, middleware y servicios de aplicación, proporcionando una referencia unificada para inspecciones diarias, evaluación de capacidad y resolución de problemas. 

Los nombres de las métricas se basan en las métricas reales del exportador recopiladas en Prometheus. Diferentes versiones del exportador pueden tener pequeñas diferencias, y la resolución de problemas real debe basarse en los resultados de las consultas en línea como referencia final. 

## Alcance 

| Categoría | Objetos Cubiertos | 
| --- | --- | 
| Monitoreo de Nodos | Hosts Linux, recursos del sistema, discos, red, procesos | 
| Monitoreo de Contenedores | Contenedores que se ejecutan en containerd, recursos de contenedores Pod | 
| Kubernetes Clúster | Nodo, Pod, Deployment, StatefulSet, Job, PVC, APIServer | 
| MySQL | MySQL instancias, conexiones, consultas, caché, bloqueos, red | 
| MongoDB | MongoDB instancias, conexiones, operaciones, memoria, red, búfer de replicación | 
| Redis | Redis instancias, clientes, comandos, memoria, Keyspace, tasa de aciertos | 
| Kafka | Broker, Tema, Partición, Grupo de Consumidores, Retraso, Réplica | 
| MinIO | Nodos del clúster, discos, Bucket, S3 solicitudes, capacidad de objetos | 
| Elasticsearch | Salud del clúster, nodos, shards, índices, JVM, pools de hilos, red |
| Servicios de Aplicación | Servidor general, llamadas de cliente, edición colaborativa, servicios RS, tiempo de ejecución |

## Reglas de Lectura de Métricas

| Tipo de Métrica | Método de Lectura | Sintaxis Común PromQL | Descripción |
| --- | --- | --- | --- |
| Contador | Observar la tasa de crecimiento o incremento dentro de una ventana de tiempo | `rate(x_total[5m])`, `increase(x_total[5m])` | El recuento de solicitudes, recuento de errores, recuento de bytes, tiempos de IO generalmente pertenecen a Contador |
| Medidor | Observar valor actual, promedio, máximo | `avg(x)`, `max(x)`, `sum(x)` | Memoria, recuento de conexiones, capacidad, valores de estado generalmente pertenecen a Medidor |
| Histograma | Observar latencia percentil | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | Latencia de solicitud, latencia de procesamiento, latencia de cola generalmente usan Histograma |
| Ratio | Mirar el porcentaje | `A / B * 100` | Utilización, tasa de error, tasa de aciertos todos pertenecen a métricas de tipo ratio |

Se recomienda no copiar directamente números fijos para los umbrales. Métricas tales como CPU, memoria, disco, cantidad de conexiones, QPS, y retraso deben evaluarse en el contexto del pico de negocio, planificación de capacidad y línea base histórica. Los comportamientos anormales en el documento se utilizan para identificar rápidamente riesgos y no equivalen a umbrales de alerta finales.

## 1. Monitoreo del Servicio del Nodo

El monitoreo de nodos se utiliza para determinar si el host está saludable, si los recursos son suficientes y si existen cuellos de botella en disco o red. Las métricas de nodo provienen principalmente de node-exporter, combinadas con el tablero de procesos del sistema para la localización a nivel de proceso.

### 1.1 Estado Básico

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Nodo Vivo | `up` | Si el exportador o el objetivo de recolección es accesible | `1` indica coleccionable, `0` indica no coleccionable | Continuamente `0` indica un problema con el nodo, la red o el exportador |
| Tiempo de Arranque | `node_boot_time_seconds` | Último tiempo de arranque del nodo | Marca de tiempo Unix | Un cambio en el tiempo de arranque indica que el nodo ha sido reiniciado |
| Información del Nodo | `node_uname_info`, `node_os_info` | Información del sistema operativo, kernel y distribución | Información de etiquetas | Se utiliza para verificar versiones del nodo, no se usa directamente como métrica de alerta |

Sugerencia para solución de problemas: verificar `up` primero, luego `node_boot_time_seconds`. Si el nodo no es coleccionable y el tiempo de arranque ha cambiado recientemente, priorizar confirmar el reinicio del host, la red ACL, y estado del proceso node-exporter.

### 1.2 CPU Métricas

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| CPU Uso | `node_cpu_seconds_total` | Tiempo acumulado que cada CPU núcleo pasa en diferentes modos | Porcentaje | `user` y `system` permanece alto a largo plazo, la potencia de cálculo del nodo es limitada |
| Inactivo CPU | `node_cpu_seconds_total{mode="idle"}` | CPU tiempo inactivo | Porcentaje | El tiempo inactivo es persistentemente bajo, lo que puede causar colas y aumentar la latencia |
| Espera de E/S | `node_cpu_seconds_total{mode="iowait"}` | Tiempo CPU espera por E/S de disco | Porcentaje | El aumento continuo en iowait generalmente indica un disco o enlace de almacenamiento más lento |
| Carga del Sistema | `node_load1`, `node_load5`, `node_load15` | Promedio de carga de 1/5/15 minutos | Valor de carga | La carga consistentemente por encima del número de CPU núcleos indica colas de tareas notables |
| CPU Presión | `node_pressure_cpu_waiting_seconds_total` | Acumulado CPU PSI tiempo de espera | Segundos/segundo | CPU La contención de recursos es significativa, los procesos están esperando CPU programación |

Consultas comunes:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

Sugerencias de investigación: Cuando CPU el uso es alto, primero diferencie entre `user`, `system`, y `iowait`. Alto `user` se debe principalmente a presión de cómputo de negocio, alto `system` puede estar relacionado con llamadas al sistema y procesamiento de paquetes de red, y alto `iowait` requiere verificar el rendimiento del disco, IOPSy la latencia.

### 1.3 Métricas de Memoria

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Memoria Total | `node_memory_MemTotal_bytes` | Memoria física total del nodo | Bytes | Usado para calcular la tasa de uso |
| Memoria Disponible | `node_memory_MemAvailable_bytes` | Memoria que el sistema puede asignar a procesos | Bytes / Porcentaje | La memoria disponible consistentemente baja es propensa a provocar OOM o reclamaciones frecuentes |
| Memoria Libre | `node_memory_MemFree_bytes` | Memoria completamente no utilizada | Bytes | No se puede usar sola en Linux para juzgar la presión de la memoria |
| Presión de Memoria | `node_pressure_memory_waiting_seconds_total` | Memoria acumulada PSI tiempo de espera | Segundos/Segundo | Aumento en la recuperación de memoria o espera de asignación |
| OOM Conteo | `node_vmstat_oom_kill` | Número de sistemas OOM terminaciones | Conteo/Incremento | Cuando aumenta, identificar los procesos terminados y el pico de memoria |

Consultas comunes:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

Sugerencia de investigación: No solo mirar `MemFree` para la memoria. La disponibilidad real debe evaluarse más mediante `MemAvailable`, combinado con la memoria del conjunto de trabajo del contenedor, registros de procesos RSS, y OOM .

### 1.4 Capacidad de disco e inodos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Sistema de archivos total | `node_filesystem_size_bytes` | Capacidad total del punto de montaje | Bytes | Usado para calcular la tasa de uso de disco |
| Sistema de archivos disponible | `node_filesystem_avail_bytes` | Espacio disponible para usuarios regulares | Bytes | La insuficiencia de espacio disponible puede causar fallos de escritura |
| Sistema de archivos libre | `node_filesystem_free_bytes` | Espacio libre en el sistema de archivos | Bytes | Incluye espacio reservado de root; generalmente considerado junto con `avail` |
| Estado de solo lectura | `node_filesystem_readonly` | Si el sistema de archivos es de solo lectura | `0/1` | Cuando `1`, las escrituras del negocio fallarán |
| Total de inodos | `node_filesystem_files` | Número total de inodos en el sistema de archivos | Conteo | Necesita atención particular en escenarios de archivos pequeños |
| Inodos restantes | `node_filesystem_files_free` | Número de inodos restantes | Conteo/Porcentaje | Cuando los inodos se agotan, no se pueden crear archivos incluso si todavía hay espacio en el disco |

Consultas comunes:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

Sugerencias de investigación: Las alertas de capacidad de disco deben verificarse por punto de montaje, especialmente para discos de datos, discos de registro y directorios del runtime de contenedores. El alto uso de inodos generalmente proviene de un gran número de archivos pequeños, secciones de registro o archivos temporales no limpiados.

### 1.5 Disco IOPS, Rendimiento y Latencia

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Lectura IOPS | `node_disk_reads_completed_total` | Número de solicitudes de lectura de disco completadas | veces/seg | Lectura IOPS cerca del límite del dispositivo, la latencia de lectura aumenta |
| Escribir IOPS | `node_disk_writes_completed_total` | Número de solicitudes de escritura en disco completadas | veces/seg | Respaldo de escritura, los registros o las confirmaciones de la base de datos se ralentizan |
| Rendimiento de lectura | `node_disk_read_bytes_total` | Bytes acumulados leídos del disco | Bytes/s | Alto rendimiento y alta espera de E/S indican que el almacenamiento está ocupado |
| Rendimiento de escritura | `node_disk_written_bytes_total` | Bytes acumulados escritos en el disco | Bytes/s | Rendimiento de escritura alto a largo plazo puede afectar bases de datos y almacenamiento de objetos |
| Tiempo de lectura | `node_disk_read_time_seconds_total` | Tiempo acumulado de las solicitudes de lectura | seg/seg | Aumenta la latencia de lectura |
| Tiempo de escritura | `node_disk_write_time_seconds_total` | Tiempo acumulado de las solicitudes de escritura | segundos/segundo | Aumento de la latencia de escritura |
| E/S ocupadas | `node_disk_io_time_seconds_total` | Tiempo acumulado que el disco pasa procesando E/S | porcentaje | Cuando la carga está cerca de lo máximo, las aplicaciones esperan por E/S |
| Tiempo de E/S ponderado | `node_disk_io_time_weighted_seconds_total` | Tiempo de E/S considerando la longitud de la cola | segundos/segundo | Acumulación en la cola indica colas severas en el dispositivo |
| Presión de E/S | `node_pressure_io_waiting_seconds_total` | E/S acumuladas PSI tiempo de espera | segundos/segundo | Los procesos pasan más tiempo esperando por E/S |

Consultas comunes:

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

Sugerencia de investigación: No solo mire la capacidad del disco al verificar problemas. Incluso si la capacidad es normal, el rendimiento del negocio puede ralentizarse cuando IOPS, el rendimiento, E/S ocupadas y espera de E/S aumentan simultáneamente. Servicios de E/S intensivos como bases de datos, Kafka, y MinIO deben centrarse en la latencia de escritura y las colas.

### 1.6 Métricas de red

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Signos anormales |
| --- | --- | --- | --- | --- |
| Tráfico entrante | `node_network_receive_bytes_total` | Bytes acumulados recibidos por la tarjeta de red | Bytes/s | Aumento repentino del tráfico entrante, posiblemente debido a picos de solicitudes o sincronización de datos |
| Tráfico saliente | `node_network_transmit_bytes_total` | Bytes acumulados enviados por la tarjeta de red | Bytes/s | Aumento repentino del tráfico saliente, posiblemente debido a descargas, copias de seguridad o replicación |
| Errores entrantes | `node_network_receive_errs_total` | Número acumulativo de paquetes de error recibidos | Recuento/s | Problemas de tarjeta de red, enlace o controlador |
| Errores salientes | `node_network_transmit_errs_total` | Número acumulativo de paquetes de error enviados | Recuento/s | Problemas de enlace o de cola de tarjeta de red |
| Pérdida de paquetes entrantes | `node_network_receive_drop_total` | Número acumulativo de paquetes descartados recibidos | Recuento/s | La cola del kernel o la tarjeta de red no puede mantenerse al día |
| Pérdida de paquetes salientes | `node_network_transmit_drop_total` | Valor acumulativo de pérdida de paquetes enviados | veces/segundo | Congestión de salida o NIC presión de cola |

Consultas comunes:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

Sugerencias de investigación: Para anomalías de red, observe el tráfico, los paquetes de error y la pérdida de paquetes juntos. Un tráfico alto por sí solo no indica necesariamente una falla; el tráfico alto acompañado de paquetes de error o pérdida de paquetes es más probable que sea un problema del enlace o de la pila de red del host.

### 1.7 TCP, Descriptores de archivo y estrés del sistema

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad / Medición común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Actual TCP Conexiones | `node_netstat_Tcp_CurrEstab` | Número actual de conexiones establecidas TCP conexiones | recuento | Un aumento repentino en las conexiones puede indicar un pico de tráfico o fuga de conexiones |
| TIME_WAIT | `node_sockstat_TCP_tw` | Número de TIME_WAIT conexiones | recuento | Demasiadas conexiones de corta duración pueden agotar los puertos o aumentar la presión del kernel |
| TCP Asignados | `node_sockstat_TCP_alloc` | Número de TCP sockets asignados | recuento | El aumento continuo en el número de sockets requiere investigar la liberación de conexiones |
| TCP En uso | `node_sockstat_TCP_inuse` | Número de TCP sockets en uso | recuento | Aumento de la presión de conexiones |
| TCP Huérfano | `node_sockstat_TCP_orphan` | Número de sockets huérfanos | recuento | El aumento anormal puede estar relacionado con el cierre anormal de conexiones |
| Descriptores de archivo usados | `node_filefd_allocated` | Número de descriptores de archivo asignados por el sistema | uds | Demasiado alto puede afectar nuevas conexiones y apertura de archivos |
| Límite de descriptores de archivo | `node_filefd_maximum` | Límite de descriptores de archivo del sistema | uds | Se utiliza para calcular la tasa de uso de handles |

Consultas comunes: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

Recomendaciones de investigación: Los handles de archivos y TCP las conexiones generalmente se consideran juntos. Cuando el número de conexiones del servidor aumenta repentinamente, si los handles del sistema están cerca de su límite, la aplicación puede experimentar fallas en la aceptación, fallas al abrir archivos o fallas en conexiones dependientes.

### 1.8 Monitoreo de procesos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Proceso CPU | `process_cpu_seconds_total` | Total CPU tiempo del proceso | segundos/segundo | Uso elevado a largo plazo CPU por un solo proceso |
| Memoria física | `process_resident_memory_bytes` | Proceso RSS memoria | Bytes | Crecimiento continuo de RSS puede indicar una fuga de memoria |
| Memoria virtual | `process_virtual_memory_bytes` | Memoria virtual del proceso | Bytes | El crecimiento anormal necesita ser evaluado junto con RSS |
| Handles abiertos | `process_open_fds` | Número de handles de archivo abiertos del proceso | recuento | El crecimiento continuo puede indicar una fuga de handles |
| Handles máximos | `process_max_fds` | Número máximo de handles de archivo que el proceso puede abrir | recuento | Se utiliza para calcular la tasa de uso de handles del proceso |
| Hora de inicio del proceso | `process_start_time_seconds` | Hora de inicio del proceso | Marca de tiempo Unix | Los cambios en la hora de inicio indican reinicio del proceso |

Recomendaciones de investigación: Las métricas del proceso se utilizan para identificar servicios específicos para problemas a nivel de nodo. Cuando el CPU nodo está alto, verifique el proceso CPU; cuando la presión de memoria del nodo es alta, verifique RSS; cuando los handles del nodo son altos, verifique `process_open_fds`. 

## 2. Monitoreo de containerd

El monitoreo de contenedores proviene principalmente de kubelet/cAdvisor, reflejando el uso de recursos de los contenedores administrados por containerd. El documento continúa usando la `container_*` nomenclatura de métricas de Prometheus, pero el runtime de contenedores subyacente durante la operación real es containerd. 

### 2.1 Contenedor CPU

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| CPU Uso | `container_cpu_usage_seconds_total` | Total CPU tiempo de uso del contenedor | núcleos/segundos | Tasa de uso cercana al límite durante mucho tiempo, posible aumento de latencia en el negocio |
| CPU Tiempo limitado | `container_cpu_cfs_throttled_seconds_total` | Tiempo total CPU está limitado por CFS | segundos/segundos | Significativo CPU La limitación indica que el límite es demasiado estricto o la carga es demasiado alta |
| CPU Cuota | `container_spec_cpu_quota` | Contenedor CPU cuota | valor de cuota | Se usa para identificar si un CPU límite está establecido |

Consultas comunes: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

Recomendación de investigación: Contenedor con alta CPU no necesariamente requiere escalamiento. Primero, verifique si está siendo limitado, luego verifique si las solicitudes/límites del Pod son demasiado bajos y finalmente, considere la latencia de solicitud del servicio para determinar si realmente afecta al negocio.

### 2.2 Memoria del Contenedor

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| RSS Memoria | `container_memory_rss` | Páginas anónimas del contenedor y RSS memoria | Bytes | Crecimiento continuo se acerca más a la presión real de memoria del proceso |
| Memoria Usada | `container_memory_usage_bytes` | Uso total de memoria del contenedor | Bytes | Incluye caché, no se puede determinar la fuga por sí sola |
| Memoria del Conjunto de Trabajo | `container_memory_working_set_bytes` | Memoria activa del conjunto de trabajo del contenedor | Bytes | Acercarse al límite puede causar OOMKilled |
| Límite de Memoria | `container_spec_memory_limit_bytes` | Límite de memoria del contenedor | Bytes | Se usa para calcular la tasa de uso de memoria |

Consultas comunes:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

Sugerencia de investigación: Para riesgos de memoria en contenedores de negocio, priorice observar el conjunto de trabajo y RSS. `usage_bytes` está ampliamente afectado por la caché de páginas, adecuado para la observación de capacidad, pero no adecuado como única base para OOM juicio.

### 2.3 Disco y Almacenamiento Temporal del Contenedor

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Rendimiento de lectura | `container_fs_reads_bytes_total` | Bytes acumulados leídos por el contenedor desde el disco | Bytes/s | Pico repentino en tráfico de lectura puede indicar escaneo, importación o extracción de origen de caché |
| Rendimiento de escritura | `container_fs_writes_bytes_total` | Bytes acumulados escritos por el contenedor en el disco | Bytes/s | Picos de escritura pueden causar presión de IO en el nodo |
| Lectura IOPS | `container_fs_reads_total` | Número de solicitudes de lectura del contenedor | Operaciones/s | Alta frecuencia de lecturas de bloques pequeños puede aumentar la espera de E/S |
| Escribir IOPS | `container_fs_writes_total` | Número de solicitudes de escritura por el contenedor | Operaciones/s | Escritura excesiva de registros o archivos temporales |
| Uso del sistema de archivos | `container_fs_usage_bytes` | Uso del sistema de archivos del contenedor | Bytes | Acumulación de archivos temporales o registros |
| Límite del sistema de archivos | `container_fs_limit_bytes` | Límite del sistema de archivos del contenedor | Bytes | Las escrituras pueden fallar al acercarse al límite |

Consultas comunes: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

Sugerencia de investigación: Cuando la escritura en disco del contenedor es anormal, primero verifique el volumen de registros del Pod, el directorio de archivos temporales y las tareas por lotes. Cuando la E/S del disco del nodo es alta, se pueden usar las métricas del FS del contenedor para localizar qué Pod está escribiendo.

### 2.4 Red del contenedor

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Tráfico entrante | `container_network_receive_bytes_total` | Total de bytes recibidos por el contenedor | Bytes/s | Aumento repentino del tráfico de solicitudes o del tráfico de replicación |
| Tráfico saliente | `container_network_transmit_bytes_total` | Total de bytes enviados por el contenedor | Bytes/s | Aumento del tráfico de descarga, sincronización, obtención desde el origen o exportación |
| Pérdida de paquetes entrantes | `container_network_receive_packets_dropped_total` | Total de paquetes descartados al recibir por el contenedor | veces/s | Pérdida de paquetes causada por la pila de red o la presión del nodo |
| Pérdida de paquetes salientes | `container_network_transmit_packets_dropped_total` | Total de paquetes descartados al transmitir por el contenedor | veces/s | Congestión de salida, NIC cola, o CNI problemas |

Consultas comunes:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

Sugerencias de investigación: La red de contenedores debe analizarse junto con el nodo NIC métricas. Si la pérdida de paquetes aumenta a nivel del Pod pero no hay anomalía en el nodo, continúe revisando CNI, iptables y la carga en el nodo donde reside el Pod. 

### 2.5 Hilos del Contenedor y Ciclo de Vida

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Número de Hilos | `container_threads` | Número de hilos dentro del contenedor | recuento | El crecimiento continuo de los hilos puede indicar una fuga de hilos |
| Última Vez Visto | `container_last_seen` | La última vez que cAdvisor vio el contenedor | Marca de tiempo Unix | No actualizarse durante mucho tiempo puede indicar que el contenedor salió o anomalía en la recolección |
| Cantidad de reinicios | `kube_pod_container_status_restarts_total` | Número total de reinicios del contenedor | contar/incrementar | Reinicios frecuentes indican fallo, falla de sondeo, o OOM |
| Razón de espera | `kube_pod_container_status_waiting_reason` | Razón por la cual el contenedor está en estado de espera | valor de etiqueta | `CrashLoopBackOff`, `ImagePullBackOff`, etc., necesitan ser abordados |
| Estado de ejecución | `kube_pod_container_status_running` | Si el contenedor está en ejecución | `0/1` | Contenedor clave no `1` indica que el servicio no está disponible |

Recomendaciones de investigación: Para anomalías del contenedor, primero revise la razón del estado, luego observe la cantidad de reinicios y la hora del reinicio más reciente. Si los reinicios son frecuentes, continúe investigando usando registros de la aplicación, OOM eventos, y configuraciones de sondeo. 

## 3. Kubernetes Monitoreo del clúster

Kubernetes El monitoreo se utiliza para evaluar el uso de recursos del clúster, la salud del plano de control, el estado de las réplicas de carga de trabajo y el estado de los objetos de almacenamiento. Las métricas principales provienen de kube-state-metrics, kubelet y APIServer. 

### 3.1 Capacidad del nodo y recursos asignables

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Capacidad del nodo | `kube_node_status_capacity` | Capacidad total del nodo | CPU, memoria, número de Pods, etc. | Usado para planificación de capacidad |
| Recursos asignables | `kube_node_status_allocatable` | Recursos asignables del nodo | CPU, memoria, número de Pods, etc. | Recursos asignables insuficientes causarán que los Pods estén pendientes |
| Condiciones del nodo | `kube_node_status_condition` | Nodo Ready, MemoryPressure y otros estados | `0/1` | Ready anormal o aparición de Pressure requiere atención inmediata |
| Programación prohibida | `kube_node_spec_unschedulable` | ¿Está el nodo Cordon? | `0/1` | Cuando se establece en '1', el nodo no programará un nuevo Pod |
| Información del Nodo | `kube_node_info` | Versión del nodo, información del kernel y del tiempo de ejecución del contenedor | Información de etiquetas | Se utiliza para solucionar diferencias de versión |

Sugerencia de solución de problemas: Cuando el pod está pendiente, primero compruebe los recursos asignables y las solicitudes, luego verifique si el nodo está 'no programable', y finalmente compruebe si la condición del nodo está experimentando presión de recursos. 

### 3.2 Estado del Pod 

| Dimensiones de Monitoreo | Indicadores | Significado de los Indicadores | Aperturas/Unidades Comunes | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Información del Pod | `kube_pod_info` | Información del namespace del pod, nodo, etc. | Información de etiquetas | Se utiliza para localizar la distribución del Pod |
| Etapa del Pod | `kube_pod_status_phase` | Estado Pendiente, En ejecución, Exitoso, Fallido, etc. | `0/1` | El aumento de Pendiente/Fallido indica anomalías en la programación o ejecución |
| Pod Listo | `kube_pod_status_ready` | ¿El Pod está Listo? | `0/1` | No estar listo afecta la disponibilidad del servicio |
| Razón del Pod | `kube_pod_status_reason` | Razón anormal del Pod | Valor de la Etiqueta | Evicted, NodeLost, etc. necesitan investigarse |
| Reinicios del Contenedor | `kube_pod_container_status_restarts_total` | Número de reinicios del contenedor | veces/incremento | El crecimiento de reinicios indica problemas de estabilidad |
| Contenedor en Espera | `kube_pod_container_status_waiting` | Si el contenedor está en estado de espera | `0/1` | Si el estado de espera persiste, el Pod no puede proporcionar el servicio normalmente |
| Razón de espera | `kube_pod_container_status_waiting_reason` | Razón del estado de espera | Valor de la Etiqueta | Error al extraer imagen, CrashLoop, etc. |
| Contenedor Terminado | `kube_pod_container_status_terminated` | Si el contenedor está terminado | `0/1` | La terminación inesperada debe verificarse junto con los reinicios y registros |

Consultas comunes:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

Sugerencia de Investigación: No solo mire la fase del pod cuando haya una anomalía en el pod. El estado Listo, la razón y la razón de espera del contenedor ilustran mejor el problema específico.

### 3.3 Solicitudes y Límites de Recursos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Comportamiento Anómalo |
| --- | --- | --- | --- | --- |
| Recursos Solicitados | `kube_pod_container_resource_requests` | Solicitudes del Contenedor | CPU, Memoria | Las solicitudes demasiado altas afectan la programación, las demasiado bajas afectan la estabilidad |
| Límites de recursos | `kube_pod_container_resource_limits` | Límites de contenedores | CPU, Memoria | Los límites demasiado bajos pueden causar CPU estrangulamiento o OOM |
| Capacidad asignable del nodo | `kube_node_status_allocatable` | Recursos disponibles para la programación en un nodo | CPU, Memoria | Se utiliza para calcular la tasa de asignación de recursos del clúster |
| Uso del contenedor | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | Uso real CPU y uso de memoria | Núcleos/segundos, Bytes | Se utiliza para determinar si las solicitudes/límites son razonables |

Consultas comunes:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

Sugerencia de investigación: La planificación de recursos debe considerar tanto el 'valor solicitado' como el 'valor de uso real.' Solo mirar las solicitudes puede juzgar mal la presión del negocio, mientras que solo mirar el uso puede pasar por alto la capacidad de programación.

### 3.4 Réplicas de carga de trabajo

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Réplicas de implementación | `kube_deployment_status_replicas` | Número actual de réplicas de implementación | unidades | Inconsistente con las réplicas esperadas |
| Réplicas Actualizadas | `kube_deployment_status_replicas_updated` | Número de réplicas actualizado a la nueva versión | unidades | Sin crecimiento durante mucho tiempo durante la liberación |
| Réplicas No Disponibles | `kube_deployment_status_replicas_unavailable` | Número de réplicas no disponibles | unidades | La capacidad del servicio disminuye cuando es mayor que 0 |
| Réplicas de StatefulSet | `kube_statefulset_status_replicas` | Número actual de réplicas de StatefulSet | unidades | Réplicas anormales en servicios stateful |
| StatefulSet Listo | `kube_statefulset_status_replicas_ready` | Número de réplicas de StatefulSet listas | unidades | Si Listo es menor que las réplicas esperadas, el servicio está incompleto |

Recomendaciones de Investigación: Cuando hay una anomalía en la liberación, verifique `updated` y `unavailable`. Para anomalías de StatefulSet, preste atención a PVC, orden de inicio de Pods y afinidad de nodos.

### 3.5 Trabajos y Tareas por Lotes

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Trabajos en Ejecución | `kube_job_status_active` | Número de trabajos actualmente activos | Conteo | Actividad prolongada puede indicar un trabajo atascado |
| Trabajos Fallidos | `kube_job_status_failed` | Número de fallos de trabajo | Conteo | Un aumento en los fallos requiere revisar los registros de trabajos |
| Trabajos exitosos | `kube_job_status_succeeded` | Número de trabajos completados con éxito | Conteo | Se utiliza para determinar la finalización de la tarea |
| Tiempo de finalización | `kube_job_status_completion_time` | Tiempo de finalización del trabajo | Marca de tiempo Unix | La falta de tiempo de finalización puede indicar trabajos incompletos |

Recomendaciones para la investigación: Cuando las tareas por lotes presentan anomalías, revise `active`, `failed`, y `succeeded` en conjunto. Solo mirar los fallos puede pasar por alto tareas que han estado atascadas por mucho tiempo.

### 3.6 PVC y Objetos de almacenamiento

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| PVC Estado | `kube_persistentvolumeclaim_status_phase` | PVC Estados Bound, Pending y otros | `0/1` | Pendiente causará que el Pod no pueda montar el almacenamiento |
| PVC Capacidad solicitada | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | La capacidad de almacenamiento solicitada por el PVC | Bytes | Se utiliza para la planificación de capacidad y la gestión de cuotas |

Sugerencia de solución de problemas: Cuando un servicio stateful falla al iniciar, además de revisar el Pod, también debe verificar si el PVC está Bound, si la clase de almacenamiento está disponible y si el almacenamiento subyacente tiene capacidad insuficiente.

### 3.7 APIServer, etcd y Plano de Control

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Cantidad de solicitudes del APIServer | `apiserver_request_total` | Número acumulado de solicitudes al APIServer | solicitudes/seg | Picos repentinos de solicitudes pueden provenir de controladores, kubectl, o componentes de negocio |
| Latencia del APIServer | `apiserver_request_duration_seconds_bucket` | Bolsas de duración de solicitudes del APIServer | P50/P95/P99 | El aumento de la latencia afectará la programación, el despliegue y la sincronización del controlador |
| Latencia de etcd | `etcd_request_duration_seconds_bucket` | Bolsas de duración de solicitudes de etcd | P50/P95/P99 | Un etcd lento puede ralentizar todo el plano de control |
| Espera en la cola | `workqueue_queue_duration_seconds_bucket` | Duración de espera en la cola del controlador | Duración percentil | Acumulación en la cola, la sincronización del estado de los recursos se ralentiza |
| Procesamiento de la cola | `workqueue_work_duration_seconds_bucket` | Duración del procesamiento del controlador | Duración percentil | El procesamiento del controlador se ralentiza |

Consultas comunes:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

Recomendaciones de investigación: Los problemas del plano de control generalmente se manifiestan como despliegue lento, actualizaciones lentas del estado de los Pods y respuestas lentas. kubectl Cuando la latencia del APIServer y la latencia de etcd aumentan simultáneamente, priorice la verificación de etcd, IO de disco y carga del nodo del plano de control.

## 4. MySQL Monitoreo

MySQL El monitoreo se utiliza para observar la disponibilidad de instancias, presión de conexiones, SQL volumen de solicitudes, consultas lentas, aciertos de caché, tablas temporales, esperas de bloqueo, manejadores de archivos y rendimiento de la red.

### 4.1 Estado de la Instancia y Volumen de Solicitudes

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Instancia Activa | `up` | Si se puede recolectar el exportador de mysql | `0/1` | Cuando `0`, instancia, red o exportador está anormal |
| Tiempo de actividad | `mysql_global_status_uptime` | MySQL Tiempo de ejecución | segundos | La disminución indica reinicio de la instancia |
| Consultas Totales | `mysql_global_status_queries` | Número acumulativo de consultas | veces/segundo | QPS Un pico puede indicar un pico de negocio o solicitudes anormales |
| Preguntas | `mysql_global_status_questions` | Número acumulativo de sentencias iniciadas por clientes | veces/segundo | Debe verse junto con las consultas para evaluar la presión de solicitudes |
| Estadísticas de Comandos | `mysql_global_status_commands_total` | Recuento acumulativo de varios comandos | veces/segundo | Puede distinguir comandos como select, insert, update, delete |

Consultas comunes: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

Sugerencias de investigación: Cuando QPS aumenta, primero revise la distribución de comandos. Si `select` aumenta junto con indicadores de tipo escaneo, preste atención a los índices y consultas lentas; si los comandos de escritura aumentan, continúe monitoreando esperas de bloqueo, IO de disco y latencia de escritura del host.

### 4.2 Conexiones y Hilos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Conexiones Actuales | `mysql_global_status_threads_connected` | Número de hilos actualmente conectados | recuento | Acercarse al límite puede causar fallos en nuevas conexiones |
| Hilos Activos | `mysql_global_status_threads_running` | Número de hilos que se están ejecutando actualmente | recuento | El aumento continuo generalmente indica ejecución lenta o espera de bloqueo SQL Ejecución lenta o espera de bloqueo |
| Máximo histórico de conexiones | `mysql_global_status_max_used_connections` | Número máximo histórico de conexiones utilizadas | recuento | Acercándose al máximo_las conexiones indican que se necesita evaluar el pool de conexiones |
| Conexiones máximas | `mysql_global_variables_max_connections` | MySQL configuración máxima de conexiones | recuento | Se utiliza para calcular la tasa de uso de conexiones |
| Clientes anormales | `mysql_global_status_aborted_clients` | Número acumulativo de desconexiones anormales de clientes | veces/seg | Problemas de red, tiempos de espera o excepciones del lado del cliente |
| Conexión fallida | `mysql_global_status_aborted_connects` | Número total de fallos de conexión | veces/segundo | Errores de autenticación, límite de conexiones, anomalías de red, etc. |

Consultas comunes:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

Sugerencias de investigación: Un alto número de conexiones no significa necesariamente que la base de datos sea lenta; también puede deberse a un pool de conexiones de la aplicación configurado incorrectamente. `Threads_running` tener un valor alto durante mucho tiempo es más preocupante, ya que generalmente corresponde a SQL problemas de ejecución o espera de bloqueo.

### 4.3 Consultas lentas, escaneos y ordenamientos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Consultas lentas | `mysql_global_status_slow_queries` | Número acumulativo de consultas lentas | veces/seg | El aumento indica más lentitud SQL |
| Escaneos completos de joins | `mysql_global_status_select_full_join` | Número de joins sin índices | veces/seg | Indica que podrían faltar índices en las condiciones de join |
| Escaneos completos de tablas | `mysql_global_status_select_scan` | Número de escaneos completos de tablas | veces/seg | Escaneos completos en tablas grandes pueden ralentizar la instancia |
| Ordenamiento por fusión | `mysql_global_status_sort_merge_passes` | Número de veces que ordenar requiere múltiples fusiones | veces/seg | Buffer de ordenamiento insuficiente o demasiados datos para ordenar |

Sugerencias de investigación: Cuando aumentan las consultas lentas, compruebe los tiempos de publicación del negocio y SQL los registros de cambios. Si aumentan las métricas de escaneo y ordenamiento, generalmente consulte los registros de lentitud, planes de ejecución y diseño de índices.

### 4.4 Pool de Memoria InnoDB

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Tamaño del Pool de Memoria | `mysql_global_variables_innodb_buffer_pool_size` | Configuración del Tamaño del Pool de Memoria InnoDB | Bytes | Demasiado pequeño aumentará las lecturas de disco |
| Páginas del Pool de Memoria | `mysql_global_status_buffer_pool_pages` | Número de diversos tipos de páginas del Pool de Memoria | Páginas | Se usa para monitorear páginas sucias, libres, de datos y otras páginas |
| Tamaño de Página | `mysql_global_status_innodb_page_size` | Tamaño de página InnoDB | Bytes | Se usa para convertir el recuento de páginas a capacidad |

Sugerencia de investigación: Cuando la tasa de aciertos del Pool de Memoria es baja, la base de datos accederá más al disco. Es necesario evaluarlo junto con el rendimiento de lectura de disco del nodo, lectura IOPSy iowait.

### 4.5 Tablas Temporales, Cachés de Tablas y Manejo de Archivos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Tabla Temporal | `mysql_global_status_created_tmp_tables` | Número Total de Tablas Temporales Creada | veces/segundo | Aumento de Complejidad de Consultas |
| Tablas Temporales en Disco | `mysql_global_status_created_tmp_disk_tables` | Número total de tablas temporales en disco creadas | veces/seg | Mayor presión de IO de disco, SQL puede desacelerar |
| Archivos Temporales | `mysql_global_status_created_tmp_files` | Número total de archivos temporales creados | veces/seg | Aumento en archivos temporales |
| Bloqueos de Tabla Inmediatos | `mysql_global_status_table_locks_immediate` | Número de veces que se adquirieron bloqueos de tabla de inmediato | veces/seg | Métrica de referencia normal |
| Bloqueos de Tabla en Espera | `mysql_global_status_table_locks_waited` | Número de veces que se esperó por bloqueos de tabla | veces/seg | Aumento de la contención de bloqueos |
| Éxitos en la Caché de Tablas | `mysql_global_status_table_open_cache_hits` | Número de éxitos en la caché de apertura de tablas | veces/seg | Un bajo número de éxitos puede indicar aperturas frecuentes de tablas |
| Fallas en la Caché de Tablas | `mysql_global_status_table_open_cache_misses` | Número de fallas en la caché de apertura de tablas | veces/seg | Se necesita evaluación de la caché de tablas |
| Desbordamientos de la Caché de Tablas | `mysql_global_status_table_open_cache_overflows` | Número de desbordamientos en la caché de apertura de tablas | veces/seg | Configuración insuficiente o demasiadas tablas |
| Tablas Abiertas | `mysql_global_status_open_tables` | Número actual de tablas abiertas | uds | El riesgo aumenta al acercarse al límite de la caché |
| Configuración de Caché de Tablas | `mysql_global_variables_table_open_cache` | tabla_abrir_valor configurado de caché | uds | Usado para calcular la tasa de uso |
| Archivos Abiertos | `mysql_global_status_open_files` | Número actual de archivos abiertos | uds | Puede afectar SQL la ejecución al acercarse al límite de archivos |
| Límite de Archivos | `mysql_global_variables_open_files_limit` | MySQL límite de descriptores de archivos | uds | Usado para calcular la tasa de uso de descriptores de archivos |

Sugerencias de Resolución de Problemas: Las tablas temporales, las esperas de bloqueo y los fallos de caché de tablas suelen aparecer junto con consultas lentas. Cuando aumentan las tablas temporales en disco, preste atención al IO de escritura del nodo, la latencia del disco y SQL la clasificación/agrupamiento.

### 4.6 Rendimiento de la Red

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Tráfico entrante | `mysql_global_status_bytes_received` | Acumulado MySQL bytes recibidos | Bytes/s | Aumento en el cuerpo de la solicitud o tráfico de escritura |
| Tráfico saliente | `mysql_global_status_bytes_sent` | Bytes acumulativos enviados por MySQL | Bytes/s | Consultas grandes, exploraciones completas de tablas y exportaciones masivas aumentarán el tráfico de salida |

Consultas comunes:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

Sugerencias de investigación: Cuando MySQL Cuando el tráfico de salida aumenta repentinamente, generalmente se debe prestar atención a grandes conjuntos de resultados, tareas de exportación y consultas sin paginación.

## 5. MongoDB Monitoreo

MongoDB El monitoreo se utiliza para observar el estado de la instancia, el recuento de conexiones, el volumen de operaciones, el escaneo de consultas, el uso de memoria, el rendimiento de la red y las condiciones del búfer de replicación.

### 5.1 Instancias y Conexiones

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Instancia Activa | `up` | Si el exportador de mongo puede recopilar datos | `0/1` | Si `0`, la instancia o el exportador están anormales |
| Tiempo de actividad | `mongodb_ss_uptime` | MongoDB Tiempo de ejecución | segundos | Valores más pequeños indican reinicio de la instancia |
| Recuento de Conexiones | `mongodb_ss_connections` | Estadísticas actuales relacionadas con la conexión | recuento | Un recuento de conexiones anormalmente alto puede indicar un pico en el grupo de conexiones o en el negocio |

Sugerencias de investigación: Cuando el recuento de conexiones aumenta, primero confirme si hay un pico de negocio, cambios en la configuración del grupo de conexiones o reconexiones anormales de clientes.

### 5.2 Operaciones y Manejo de Documentos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Recuento de Operaciones | `mongodb_ss_opcounters` | El número acumulado de operaciones como insertar, consultar, actualizar, eliminar | veces/segundo | Un aumento repentino en un cierto tipo de operación indica un cambio en los patrones de acceso del negocio |
| Manejo de Documentos | `mongodb_ss_metrics_document` | Recuento acumulativo de documentos insertados, actualizados, eliminados, devueltos, etc. | veces/segundo | Si el número de devueltos es significativamente mayor de lo realmente necesario, el conjunto de resultados puede ser demasiado grande |
| Entradas de Índice Escaneadas | `mongodb_ss_metrics_queryExecutor_scanned` | Número de entradas de índice escaneadas durante las consultas | veces/segundo | El escaneo excesivo puede indicar un indexado incorrecto |
| Documentos Escaneados | `mongodb_ss_metrics_queryExecutor_scannedObjects` | Número de documentos escaneados durante las consultas | veces/segundo | El escaneo elevado de documentos indica baja eficiencia en las consultas |

Consultas comunes: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

Recomendaciones de Investigación: Una manifestación común de MongoDB consultas lentas es un aumento en escaneados/objetosEscaneados. Es necesario analizar en combinación con registros lentos y aciertos de índice.

### 5.3 Memoria, Red y Disco

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad/Medición Común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Memoria Resident | `mongodb_ss_mem_resident` | MongoDB memoria residente | MB o Bytes | Un aumento continuo requiere revisar la memoria del host |
| Memoria virtual | `mongodb_ss_mem_virtual` | MongoDB memoria virtual | MB o Bytes | El aumento por sí solo no necesariamente indica presión real |
| Tráfico entrante | `mongodb_ss_network_bytesIn` | MongoDB bytes recibidos acumulativos | Bytes/s | Aumento en el tráfico de escritura o solicitudes |
| Tráfico saliente | `mongodb_ss_network_bytesOut` | MongoDB bytes enviados acumulativos | Bytes/s | Consultas grandes o tareas de exportación que causan aumento del tráfico de salida |
| Lectura IO del Host | `node_disk_reads_completed_total` | Lectura IOPS en el nodo donde MongoDB reside | veces/s | Consultas que escanean provocando aumento de lectura IO |
| Escritura IO del Host | `node_disk_writes_completed_total` | Escribir IOPS en el nodo donde MongoDB está ubicado | veces/seg | Aumento de presión de escritura o del journal | 

Sugerencia de Resolución de Problemas: MongoDB El rendimiento de memoria y disco debe considerarse junto con la memoria del nodo y el IO de disco. Visualizar métricas de la instancia junto con la lectura/escritura de disco del host facilita determinar si MongoDB la instancia en sí es lenta o si los recursos subyacentes son lentos. 

### 5.4 Búfer de Replicación 

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal | 
| --- | --- | --- | --- | 
| Tamaño del Búfer de Replicación | `mongodb_ss_metrics_repl_buffer_sizeBytes` | Tamaño del búfer de replicación | Bytes | El crecimiento continuo del búfer indica que el consumo de replicación no es oportuno | 

Sugerencia de solución de problemas: Los búferes de replicación anormales generalmente están relacionados con la capacidad de procesamiento del esclavo, la red o las escrituras en disco. Deben analizarse junto con la demora de replicación, la red del nodo y las métricas de escritura en disco. 

## 6. Redis Monitoreo 

Redis El monitoreo se usa para observar la disponibilidad de la instancia, el número de conexiones, el procesamiento de comandos, los niveles de memoria, el espacio de claves, la tasa de aciertos, la expulsión y el rendimiento de la red. 

### 6.1 Instancia y Clientes 

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal | 
| --- | --- | --- | --- | --- |
| Instancia Activa | `up` | Si Redis El exportador puede ser recopilado | `0/1` | Cuando `0`, la instancia o el exportador están anormales |
| Tiempo de actividad | `redis_uptime_in_seconds` | Redis Tiempo de ejecución | segundos | Disminuir indica reinicio de la instancia |
| Conexiones de Clientes | `redis_connected_clients` | Número actual de conexiones de clientes | recuento | Un aumento repentino puede indicar un grupo de conexiones o una tormenta de reconexión |

### 6.2 Comandos, Memoria y Espacio de Claves

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Comandos Procesados | `redis_commands_processed_total` | Número total de Redis comandos procesados | veces/segundo | Pico repentino QPS puede aumentar la instancia CPU |
| Clasificación de Comandos | `redis_commands_total` | Número total de comandos por tipo | veces/segundo | Puede identificar cambios en los comandos get, set, del, etc. |
| Memoria Usada | `redis_memory_used_bytes` | Actual Redis uso de memoria | Bytes | Acercarse a maxmemory puede activar la expulsión |
| Memoria Máxima | `redis_memory_max_bytes` | Redis configuración de maxmemory | Bytes | Se usa para calcular la tasa de uso de memoria |
| Número de Claves | `redis_db_keys` | Número de claves en cada BD | recuento | Un crecimiento anormal de claves puede indicar caché sin expiración o anomalías de escritura |
| Claves con Expiración | `redis_db_keys_expiring` | Número de claves con expiración establecida | recuento | Una proporción baja requiere atención al ciclo de vida de la caché |

Consultas comunes:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 Tasa de aciertos, Expulsión y Red

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Recuento de Aciertos | `redis_keyspace_hits_total` | Número total de aciertos de claves | veces/s | Calcular la tasa de aciertos junto con los fallos |
| Recuento de Fallos | `redis_keyspace_misses_total` | Número total de fallos de clave | veces/s | El aumento de fallos puede conducir a mayor presión hacia la fuente |
| Claves expiradas | `redis_expired_keys_total` | Número total de claves expiradas | veces/s | Las tormentas de expiración pueden causar CPU jitters |
| Claves expulsadas | `redis_evicted_keys_total` | Número total de claves expulsadas | veces/s | El crecimiento indica presión de memoria o maxmemory insuficiente |
| Tráfico entrante | `redis_net_input_bytes_total` | Total de bytes recibidos por Redis | Bytes/s | Aumento en el tráfico de escritura o solicitudes |
| Tráfico saliente | `redis_net_output_bytes_total` | Total de bytes enviados por Redis | Bytes/s | El alto tráfico saliente es causado por valores grandes o lecturas por lotes |

Consultas comunes:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

Recomendación de investigación: Para Redis, concéntrese en riesgos de memoria y expulsión. Una disminución en la tasa de aciertos transferirá presión a la base de datos backend. Un aumento en las expulsiones indica que se debe evaluar la capacidad de caché o la estrategia de expulsión.

## 7. Kafka Monitoreo

Kafka El monitoreo se utiliza para observar el número de Brokers, estado de Topic/Partition, offsets de producción y consumo, retraso del Grupo de Consumidores, recuento de miembros y estado de sincronización de réplicas.

### 7.1 Broker, Topic y Partition

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Número de Brokers | `kafka_brokers` | Número de Brokers actualmente visibles | uds | Una disminución en el número indica que el Broker no está disponible o no se puede acceder al exportador |
| Particiones del Topic | `kafka_topic_partitions` | Número de Particiones de un Topic | uds | Los cambios en las particiones afectan la concurrencia y la capacidad de consumo |
| Offset actual de la partición | `kafka_topic_partition_current_offset` | Último offset de la partición | offset / tasa de crecimiento | Debe aumentar continuamente durante las escrituras de producción en curso |
| Offset más antiguo de la partición | `kafka_topic_partition_oldest_offset` | Offset más antiguo de la partición | offset | Se utiliza para observar el rango de datos retenidos |

Consultas comunes: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

Sugerencia de investigación: Cuando la tasa de producción es anormal, primero revise el crecimiento actual del desplazamiento del tema. Si el negocio confirma que hay escrituras pero el desplazamiento no aumenta, verifique errores en el lado del productor, el estado del Broker y la configuración del tema.

### 7.2 Grupo de consumidores y retraso

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Desplazamiento de consumo | `kafka_consumergroup_current_offset` | Desplazamiento actual consumido por el Grupo de Consumidores | offset / tasa de crecimiento | La falta de crecimiento indica que el consumo se ha detenido o está atascado |
| Retraso de partición | `kafka_consumergroup_lag` | Acumulación del Grupo de Consumidores en la partición | recuento | El aumento del retraso indica que el consumo está quedando rezagado respecto a la producción |
| Retraso total del grupo | `kafka_consumergroup_lag_sum` | Acumulación total del Grupo de Consumidores | recuento | El aumento continuo del retraso total indica un retraso creciente en el negocio |
| Miembros del grupo | `kafka_consumergroup_members` | Número de miembros en el Grupo de Consumidores | recuento | La disminución en el número de miembros puede llevar a una reducción en la capacidad de consumo |

Consultas comunes:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

Sugerencias para la resolución de problemas: La métrica principal del negocio de Kafka es el Retraso (Lag). Cuando el Retraso aumenta, primero verifique si el número de miembros consumidores ha disminuido, luego vea si la tasa de consumo ha bajado, y finalmente revise el tiempo de procesamiento de la aplicación, las dependencias descendentes y la entrada/salida del Broker.

### 7.3 Réplicas y ISR

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Número de Réplicas | `kafka_topic_partition_replicas` | Número de Réplicas de Partición | recuento | Menos réplicas de las esperadas reducen la fiabilidad |
| ISR Réplicas | `kafka_topic_partition_in_sync_replica` | Número de Réplicas de Partición en Sincronización | recuento | Una caída en ISR indica réplicas retrasadas o problemas del Broker |
| Líder Preferido | `kafka_topic_partition_leader_is_preferred` | Si el líder es la réplica preferida | `0/1` | El desequilibrio a largo plazo puede causar alta presión en algunos Brokers |

Sugerencias para la resolución de problemas: Una caída en ISR representa un riesgo de confiabilidad más que un retraso ordinario. Verifique el estado del corredor, la red, la latencia de escritura en disco y la sincronización de réplicas.

## 8. MinIO Monitoreo de Almacenamiento de Objetos

MinIO el monitoreo se utiliza para observar la disponibilidad del clúster de almacenamiento de objetos, el estado de los nodos y discos, la capacidad de los Buckets, S3 las solicitudes, errores, tráfico, manejadores de procesos y actividades de tareas de reparación. 

### 8.1 Nodos y Discos del Clúster 

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Nodos en Línea | `minio_cluster_nodes_online_total` | Número de nodos en línea MinIO nodos | uds | Una disminución en el número indica que los nodos no están disponibles |
| Nodos Fuera de Línea | `minio_cluster_nodes_offline_total` | Número de nodos fuera de línea MinIO nodos | uds | Mayor que 0 requiere atención a la disponibilidad del clúster |
| Discos en Línea | `minio_cluster_disk_online_total` | Número de discos en línea | uds | Una disminución en los discos afecta la redundancia y la capacidad de escritura |
| Discos Fuera de Línea | `minio_cluster_disk_offline_total` | Número de discos fuera de línea | uds | Mayor que 0 requiere solución de problemas de discos o montajes |
| Capacidad utilizable | `minio_cluster_capacity_usable_free_bytes` | Capacidad utilizable del clúster | Bytes | La disminución continua indica riesgo de capacidad insuficiente |

Sugerencia de solución de problemas: Para almacenamiento de objetos, primero verifique el estado en línea de los nodos y discos. No evalúe los discos fuera de línea solo por cantidad; el riesgo debe juzgarse en combinación con la estrategia de redundancia del código de borrado. 

### 8.2 Capacidad del cubo y recuento de objetos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Capacidad del cubo | `bucket_usage_size` | Capacidad utilizada del cubo | Bytes | Crecimiento rápido de la capacidad, se necesita evaluar la expansión |
| Recuento de objetos | `bucket_objects_count` | Número de objetos en el cubo | Conteo | Demasiados objetos pequeños aumentan la presión sobre metadatos y escaneo |
| Distribución del tamaño de los objetos | `minio_bucket_objects_size_distribution` | Distribución de los tamaños de objetos en el cubo | Estadísticas por cubo | Los cambios en la distribución de objetos afectan el rendimiento del almacenamiento y de las solicitudes |

Consultas comunes:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

Recomendaciones de investigación: El crecimiento de la capacidad debe analizarse por separado por Bucket. Cuando el número de objetos crece rápidamente pero el crecimiento de la capacidad no es evidente, generalmente se debe a un aumento en objetos pequeños. Se debe prestar atención a la limpieza del ciclo de vida y al patrón de escritura del negocio.

### 8.3 S3 Solicitudes, Errores y Tráfico

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| S3 Conteo de Solicitudes | `minio_s3_requests_total` | Conteo acumulativo de S3 API solicitudes | veces/segundo | Aumento repentino en las solicitudes, puede ser pico de negocio o reintentos |
| S3 Conteo de Errores | `minio_s3_requests_errors_total` | Conteo acumulativo de S3 API errores | veces/segundo | Incremento en la tasa de errores que afecta la lectura/escritura de objetos |
| Tráfico Recibido | `minio_s3_traffic_received_bytes` | Acumulado S3 bytes recibidos | Bytes/s | Incremento en el tráfico de carga |
| Tráfico Enviado | `minio_s3_traffic_sent_bytes` | Acumulado S3 bytes enviados | Bytes/s | Incremento en el tráfico de descarga o recuperación desde origen |

Consultas comunes:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

Recomendación de investigación: Cuando la S3 tasa de errores aumente, primero desglósela por API tipo, luego verifique el Bucket correspondiente, el estado del disco del nodo y el tráfico de red.

### 8.4 Procesos del Nodo, Handles de Archivos y IO

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Uso del Disco del Nodo | `minio_node_disk_used_bytes` | Uso del disco del MinIO nodo | Bytes | Desequilibrio de capacidad en un solo nodo |
| Handles de Archivos Abiertos | `minio_node_file_descriptor_open_total` | Número de handles de archivo abiertos por MinIO proceso | Conteo | Las solicitudes pueden fallar al acercarse al límite del sistema |
| Llamadas al Sistema de Lectura | `minio_node_syscall_read_total` | Conteo acumulativo de llamadas al sistema de lectura | Veces/seg | Aumento anormal en llamadas de lectura |
| Llamadas al Sistema de Escritura | `minio_node_syscall_write_total` | Conteo acumulativo de llamadas al sistema de escritura | Veces/seg | Aumento anormal en llamadas de escritura |
| Bytes Leídos por el Proceso | `minio_node_io_rchar_bytes` | Bytes acumulativos leídos por el proceso | Bytes/s | Incremento en la carga de lectura |
| Bytes Escritos por el Proceso | `minio_node_io_wchar_bytes` | Bytes acumulativos escritos por el proceso | Bytes/s | Incremento en la carga de escritura |
| Número de goroutines | `minio_node_go_routine_total` | Número de goroutines en el MinIO proceso | Conteo | El crecimiento continuo puede indicar acumulación de solicitudes o fuga |
| Hora de inicio | `minio_node_process_starttime_seconds` | MinIO hora de inicio del proceso | Marca de tiempo Unix | Los cambios indican reinicio del proceso |

Sugerencia de investigación: Para MinIO problemas de rendimiento, considere S3 solicitudes, discos de nodo, E/S del proceso y goroutines juntos. Un volumen alto de solicitudes por sí solo no es necesariamente anormal; la tasa de errores, la latencia de E/S y el estado fuera de línea del disco son señales de riesgo más claras.

### 8.5 Actividades de recuperación y uso

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Actividad de recuperación | `minio_heal_time_last_activity_nano_seconds` | Última hora de actividad de recuperación | Marca de tiempo en nanosegundos | Recuperaciones largas o frecuentes requieren atención a la salud del disco |
| Actividad de uso | `minio_usage_last_activity_nano_seconds` | Última hora de actividad de análisis de uso | Marca de tiempo en nanosegundos | Los análisis de uso anormales pueden afectar la precisión de las estadísticas de capacidad |

Sugerencia de investigación: Después de la recuperación anormal de un nodo o disco, supervise si las actividades de curación están progresando normalmente para evitar que la redundancia de objetos permanezca en riesgo durante mucho tiempo.

## 9. Elasticsearch Monitoreo

Elasticsearch El monitoreo se utiliza para observar la salud del clúster de búsqueda, el número de nodos, la distribución de fragmentos, las operaciones de lectura/escritura de índices, la caché, JVM, los grupos de hilos, el disco y la red. Las fallas de ES generalmente no se determinan por una única métrica; más comúnmente, "anomalías en fragmentos   JVM presión   rechazos en grupos de hilos   marcas de agua del disco" aparecen juntas.

### 9.1 Salud del Clúster y Nodos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Salud del Clúster | `elasticsearch_cluster_health_status` | Estado de salud del clúster de ES | Valor de estado | El amarillo/rojo indica anomalías en replicación o fragmento primario |
| Número de nodos | `elasticsearch_cluster_health_number_of_nodes` | Número de nodos del clúster | Conteo | La disminución del número de nodos puede indicar que un nodo está fuera de línea |
| Número de nodos de datos | `elasticsearch_cluster_health_number_of_data_nodes` | Número de nodos de datos en el clúster | Conteo | Una disminución en los nodos de datos afecta la capacidad de los fragmentos y la capacidad de lectura/escritura |
| Tareas Pendientes | `elasticsearch_cluster_health_number_of_pending_tasks` | Número de tareas pendientes en el clúster | Conteo | El crecimiento continuo indica que las actualizaciones del maestro o del estado del clúster son lentas |
| Fragmentos Primarios Activos | `elasticsearch_cluster_health_active_primary_shards` | Número de fragmentos primarios activos | uds | Alto riesgo si disminuye, puede afectar la disponibilidad del índice |
| Fragmentos Activos | `elasticsearch_cluster_health_active_shards` | Número total de fragmentos activos | uds | La disminución indica que los fragmentos no se han recuperado completamente |
| Fragmentos Inicializando | `elasticsearch_cluster_health_initializing_shards` | Número de fragmentos en inicialización | uds | La ausencia de disminución durante mucho tiempo indica una recuperación lenta |
| Fragmentos Reubicándose | `elasticsearch_cluster_health_relocating_shards` | Número de fragmentos en reubicación | uds | Demasiadas reubicaciones aumentan la presión de la red y del disco |
| Fragmentos No Asignados | `elasticsearch_cluster_health_unassigned_shards` | Número de fragmentos no asignados | uds | Mayor que 0 indica que los fragmentos no están asignados a ningún nodo |
| No Asignados con Retraso | `elasticsearch_cluster_health_delayed_unassigned_shards` | Número de fragmentos no asignados con retraso | uds | Esperando reasignación tras la desconexión de un nodo |

Consultas comunes: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

Sugerencias de investigación: ES debe primero verificar el estado de salud y los fragmentos no asignados. El estado rojo debe priorizar el manejo de fragmentos primarios; el amarillo se debe principalmente a réplicas no asignadas, las cuales tampoco se pueden dejar sin atender por mucho tiempo. 

### 9.2 Capacidad de Disco y Sistema de Archivos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medición / Unidad Común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Disco de Datos Total | `elasticsearch_filesystem_data_size_bytes` | Capacidad total del directorio de datos de ES | Bytes | Usado para calcular la tasa de uso de disco |
| Disco de Datos Disponible | `elasticsearch_filesystem_data_available_bytes` | Capacidad disponible del directorio de datos de ES | Bytes | El espacio disponible insuficiente puede desencadenar migración de fragmentos o restricciones de escritura |

Consultas comunes:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

Sugerencia de investigación: ES es muy sensible al uso del disco. Cuando el uso del disco es demasiado alto, pueden ocurrir migraciones de fragmentos, índices de solo lectura o fallas de escritura. Es necesario supervisar el crecimiento del índice, las políticas de retención y la distribución del disco de los nodos.

### 9.3 Documentos, Índices y Eliminaciones

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad Común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Conteo de Documentos | `elasticsearch_indices_docs` | Número actual de documentos | recuento | El rápido crecimiento continuo de documentos requiere evaluación de capacidad |
| Documentos Eliminados | `elasticsearch_indices_docs_deleted` | Número de documentos eliminados | recuento | Una alta tasa de eliminación puede causar presión de fusión |
| Conteo de Escrituras de Índice | `elasticsearch_indices_indexing_index_total` | Conteo acumulativo de operaciones de índice | veces/seg | Un aumento repentino en escrituras incrementa CPU, disco y presión de actualización |
| Tiempo de Escritura de Índice | `elasticsearch_indices_indexing_index_time_seconds_total` | Tiempo acumulativo de operaciones de índice | seg/seg | El aumento en el tiempo de escritura ralentiza la ruta de escritura |
| Conteo de Operaciones de Eliminación | `elasticsearch_indices_indexing_delete_total` | Conteo acumulativo de operaciones de eliminación | veces/seg | Un aumento repentino en eliminaciones puede causar presión de fusión de segmentos |
| Duración de Operación de Eliminación | `elasticsearch_indices_indexing_delete_time_seconds_total` | Duración acumulativa de operaciones de eliminación | segundos/segundo | Aumento en la duración de eliminación |

Consultas comunes:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

Recomendación de Solución de Problemas: Cuando las escrituras son lentas, no solo mire la escritura QPS. También debe considerar actualización, fusión, translog, rechazos de grupo de hilos y E/S de disco.

### 9.4 Consultas y Solicitudes Get

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Número de Solicitudes de Consulta | `elasticsearch_indices_search_query_total` | Número acumulativo de consultas de búsqueda | veces/segundo | Aumento repentino en consultas |
| Latencia de Consulta | `elasticsearch_indices_search_query_time_seconds` | Tiempo acumulativo de consultas de búsqueda | segundos/segundo | Aumento en la latencia promedio de consultas |
| Número de Solicitudes de Recuperación | `elasticsearch_indices_search_fetch_total` | Número acumulativo en la fase de recuperación de búsqueda | veces/segundo | Conjuntos de resultados grandes pueden aumentar el conteo de recuperación |
| Latencia de Recuperación | `elasticsearch_indices_search_fetch_time_seconds` | Tiempo acumulativo de recuperación de búsqueda | segundos/segundo | La recuperación lenta generalmente está relacionada con conjuntos de resultados grandes, disco o red |
| Número de Solicitudes Get | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Conteo acumulativo de aciertos y fallos de Get | veces/segundo | Un aumento en los fallos puede indicar acceso de negocio a documentos inexistentes |
| Duración de Get | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Tiempo acumulativo de solicitudes Get | segundos/segundo | Slow Get indica una presión creciente en la ruta de lectura |

Consultas comunes: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

Recomendaciones de Solución de Problemas: Las consultas lentas deben distinguirse entre consulta y obtención. Las consultas lentas están más relacionadas con las condiciones de la consulta, la estructura del índice y la presión de los fragmentos; las obtenciones lentas son más comunes cuando hay muchos campos devueltos, conjuntos de resultados grandes o lecturas lentas del disco.

### 9.5 Segmento, Fusión, Refresco y Translog

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Calibre/Unidad común | Síntomas Anormales |
| --- | --- | --- | --- | --- |
| Número de Segmentos | `elasticsearch_indices_segments_count` | Número actual de segmentos | recuento | Demasiados segmentos pueden afectar las consultas y la memoria |
| Memoria del Segmento | `elasticsearch_indices_segments_memory_bytes` | Memoria ocupada por los segmentos | Bytes | El aumento continuo puede presionar JVM |
| Número de Fusiones | `elasticsearch_indices_merges_total` | Número acumulativo de operaciones de fusión | veces/seg | Fusiones frecuentes indican alta presión de escritura o eliminación |
| Número de Documentos en la Fusión | `elasticsearch_indices_merges_docs_total` | Número acumulativo de documentos procesados por fusiones | conteo/segundo | Carga de trabajo de fusiones en aumento |
| Volumen de Datos de Fusión | `elasticsearch_indices_merges_total_size_bytes_total` | Datos acumulativos procesados por la fusión | Bytes/s | Las fusiones grandes pueden saturar la E/S del disco |
| Duración de la Fusión | `elasticsearch_indices_merges_total_time_seconds_total` | Tiempo acumulativo dedicado a fusiones | Segundos/segundo | Fusiones lentas pueden afectar el rendimiento de escritura y consulta |
| Conteo de Refrescos | `elasticsearch_indices_refresh_total` | Número acumulativo de refrescos | Veces/segundo | Refrescos frecuentes aumentan la sobrecarga |
| Duración del Refresco | `elasticsearch_indices_refresh_time_seconds_total` | Tiempo acumulativo de refresco | Segundos/segundo | Refrescos lentos afectan la visibilidad casi en tiempo real |
| Conteo de Flush | `elasticsearch_indices_flush_total` | Número acumulativo de flushes | Veces/segundo | Flushes frecuentes pueden estar relacionados con la presión de translog y escritura |
| Duración del Flush | `elasticsearch_indices_flush_time_seconds` | Tiempo acumulativo de flush | Segundos/segundo | Flushes lentos pueden afectar la estabilidad |
| Operaciones del Translog | `elasticsearch_indices_translog_operations` | Número actual de operaciones de translog | recuento | La acumulación continua requiere atención al flush |
| Tamaño del Translog | `elasticsearch_indices_translog_size_in_bytes` | Tamaño actual del translog | Bytes | Un tamaño excesivo puede afectar el tiempo de recuperación |
| Limitación de Almacenamiento | `elasticsearch_indices_store_throttle_time_seconds_total` | Tiempo acumulado de limitación del almacenamiento del índice | segundos/segundo | Limitación aumentada, escrituras afectadas por el disco |

Sugerencia de investigación: Cuando la presión de escritura es alta, la fusión, actualización, vaciado y cambio de translog ocurren juntos. Un aumento en el tiempo de fusión y limitación del almacenamiento generalmente indica que el disco ha empezado a afectar a ES.

### 9.6 Caché y Disyuntor

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidad/Medición Común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Memoria de Caché de Consultas | `elasticsearch_indices_query_cache_memory_size_bytes` | Memoria usada por la caché de consultas | Bytes | Un uso excesivo puede reducir la JVM |
| Expulsiones de caché de consultas | `elasticsearch_indices_query_cache_evictions` | Número acumulado de expulsiones de caché de consultas | veces/segundo | Expulsiones frecuentes indican caché inestable |
| Memoria Fielddata | `elasticsearch_indices_fielddata_memory_size_bytes` | Memoria usada por fielddata | Bytes | El uso elevado de fielddata puede desencadenar fácilmente presión de memoria |
| Expulsiones de fielddata | `elasticsearch_indices_fielddata_evictions` | Número acumulado de expulsiones de fielddata | veces/segundo | Alta presión de consultas o agregaciones |
| Expulsiones de caché de filtros | `elasticsearch_indices_filter_cache_evictions` | Número acumulado de expulsiones de caché de filtros | veces/segundo | Invalidación frecuente de la caché de filtros |
| Tamaño estimado del disyuntor | `elasticsearch_breakers_estimated_size_bytes` | Memoria estimada del disyuntor | Bytes | Las consultas pueden ser rechazadas al acercarse al límite |
| Límite del disyuntor | `elasticsearch_breakers_limit_size_bytes` | Límite del disyuntor | Bytes | Usado para calcular la tasa de uso del disyuntor |
| Disparo del disyuntor | `elasticsearch_breakers_tripped` | Número de veces que se disparó el disyuntor | veces/incremento | Descripción del crecimiento: solicitudes bloqueadas debido a riesgo de memoria |

Consultas comunes: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

Recomendaciones de investigación: Las consultas de agregación, ordenamiento y consultas con scripts pueden aumentar fácilmente el uso de fielddata y del disyuntor. Cuando el disyuntor se dispara, generalmente es necesario limitar el tamaño de la consulta, optimizar el mapeo del índice o ajustar el método de consulta.

### 9.7 JVM, CPU, y Carga

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| JVM Memoria Usada | `elasticsearch_jvm_memory_used_bytes` | Actual JVM memoria utilizada | Bytes | Continuamente cerca del límite, aumento de la presión de GC |
| JVM Memoria Máxima | `elasticsearch_jvm_memory_max_bytes` | Máximo disponible JVM memoria | Bytes | Utilizado para calcular JVM uso |
| JVM Memoria Comprometida | `elasticsearch_jvm_memory_committed_bytes` | JVM memoria comprometida | Bytes | Observar JVM asignación de memoria |
| JVM Pico del Pool de Memoria | `elasticsearch_jvm_memory_pool_peak_used_bytes` | Uso máximo de cada pool de memoria | Bytes | Pico alto en la generación antigua necesita atención |
| Conteo de GC | `elasticsearch_jvm_gc_collection_seconds_count` | Número de ocurrencias de GC | veces/segundo | GC frecuente, la latencia puede fluctuar |
| Tiempo de GC | `elasticsearch_jvm_gc_collection_seconds_sum` | Tiempo total de GC | seg/seg | Tiempo alto de GC puede afectar consultas y escrituras |
| Proceso CPU | `elasticsearch_process_cpu_percent` | Proceso de ES CPU uso | porcentaje | Prolongado alto CPU puede indicar carga pesada de consultas o escrituras |
| Carga del Sistema | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | Carga del nodo 1/5/15 minutos | valor de carga | Carga mayor que CPU núcleos indica encolamiento obvio de tareas |
| Conteo de Archivos Abiertos | `elasticsearch_process_open_files_count` | Número de archivos abiertos por el proceso ES | recuento | Acercarse a los límites del sistema puede afectar el acceso a archivos de índice |

Consultas comunes: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

Sugerencia de Investigación: ES más grande JVM la memoria no siempre es mejor. JVM uso, tiempo de GC, fielddata, caché de consultas y breakers deben ser monitoreados juntos para determinar si la presión de memoria es causada por consultas o un desajuste entre el tamaño del heap y la escala de datos.

### 9.8 Pool de Hilos y Red

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Medida/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Hilos Activos | `elasticsearch_thread_pool_active_count` | Número de hilos activos en el pool de hilos | Conteo | Hilos activos altos a largo plazo indican presión de procesamiento pesada |
| Tareas Completadas | `elasticsearch_thread_pool_completed_count` | Número acumulativo de tareas completadas por el pool de hilos | Veces/Segundo | Usado para observar el rendimiento de procesamiento |
| Tareas Rechazadas | `elasticsearch_thread_pool_rejected_count` | Número acumulativo de tareas rechazadas por el pool de hilos | Veces/Segundo | El crecimiento indica que el pool de hilos o la cola está llena |
| Tráfico entrante | `elasticsearch_transport_rx_size_bytes_total` | Bytes acumulativos recibidos por transporte | Bytes/s | Comunicación entre nodos aumentada o tráfico de solicitudes |
| Tráfico saliente | `elasticsearch_transport_tx_size_bytes_total` | Bytes acumulados enviados por el transporte | Bytes/s | Aumento del tráfico debido a la reubicación de fragmentos, consultas o replicación |

Consultas comunes: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

Sugerencia de investigación: El rechazo del grupo de hilos es una señal de riesgo de negocio muy directa. Para rechazos de escritura, verifique el grupo de hilos bulk/index; para rechazos de búsqueda, verifique el grupo de hilos de búsqueda, y luego determine los cuellos de botella en combinación con CPU, JVM, y la E/S de disco.

## 10. Monitoreo del Servicio de Aplicaciones

El monitoreo de aplicaciones cubre solicitudes comunes del lado del servidor, llamadas a dependencias del lado del cliente, recursos de ejecución, enlaces comerciales de edición colaborativa y tareas del servicio RS. El enfoque de las métricas de la aplicación no son los umbrales de recursos individuales, sino el volumen de solicitudes, errores, latencia y salud de las dependencias.

### 10.1 Métricas Comunes del Lado del Servidor

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| Tiempo de Actividad del Servicio | `up` | Si el exportador de aplicaciones o el endpoint de métricas es coleccionable | `0/1` | `0` significa que las métricas no son accesibles o que el servicio es anormal |
| Información de la Compilación | `ego_build_info` | Versión de compilación de la aplicación, rama y otra información | Información de etiquetas | Se utiliza para verificar la versión liberada |
| Conteo de Inicio | `ego_server_started_total` | Número acumulado de inicios del servidor | veces/incremento | El aumento indica reinicio del proceso o liberación |
| Solicitudes del Servidor | `ego_server_handle_total` | Número acumulado de solicitudes del servidor | veces/segundo | Un aumento o disminución repentina de solicitudes debe ser juzgado en combinación con el contexto del negocio |
| Consumo de Tiempo del Lado del Servidor | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | Estadísticas de Tiempo de Solicitud del Lado del Servidor | P50/P95/P99 | El aumento de la latencia afecta la experiencia del usuario | 

Consultas comunes: 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

Sugerencias de investigación: Para anomalías del lado del servidor, primero verifique si el volumen de solicitudes ha cambiado, luego observe los errores y la latencia. Si la latencia aumenta pero los recursos no son altos, continúe examinando las llamadas a dependencias descendentes y las colas.

### 10.2 Llamadas de dependencias del cliente

| Dimensión de monitoreo | Métrica | Significado de la métrica | Granularidad/unidad común | Comportamiento anormal |
| --- | --- | --- | --- | --- |
| Volumen de llamadas del cliente | `ego_client_handle_total` | El número de veces que la aplicación llama a downstream como cliente | veces/segundo | Aumento repentino en el volumen de llamadas downstream, lo que puede amplificar la presión de la dependencia |
| Latencia del cliente | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | Estadísticas de latencia de llamadas downstream | P50/P95/P99 | Un downstream lento puede ralentizar el servicio actual |
| Estado del cliente | `ego_client_stats_gauge` | Métricas del tipo de pool de conexiones o estado del cliente | Valor actual | Agotamiento del pool de conexiones, conexiones inactivas anormales, etc. |
| Kafka Latencia de producción | `kafka_produce_duration_seconds_bucket` | Tiempo que tarda la aplicación en producir Kafka Mensajes | P50/P95/P99 | Aumento en la latencia de producción, posiblemente debido a problemas en el Broker o de red |

Consultas comunes:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

Sugerencia de investigación: Cuando una interfaz de negocio es lenta, compare el tiempo consumido en el lado del servidor con el tiempo consumido por las dependencias del cliente. Si la proporción de tiempo del cliente es alta, priorice la revisión de los servicios downstream, middleware o red correspondientes.

### 10.3 Tiempo de ejecución y procesos

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Estándar/Unidad Común | Manifestación Anormal |
| --- | --- | --- | --- | --- |
| Goroutine de Go | `go_goroutines` | Número de goroutines en el proceso Go | Conteo | El crecimiento continuo puede indicar bloqueo o fuga |
| Duración de GC de Go | `go_gc_duration_seconds` | Duración de GC de Go | Segundos/Percentil | El aumento del tiempo de GC puede afectar la latencia |
| Memoria Heap de Go | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Asignación y uso del heap de Go | Bytes | El crecimiento continuo requiere verificar fugas de memoria |
| Memoria del sistema de Go | `go_memstats_sys_bytes` | Memoria solicitada por el runtime de Go al sistema | Bytes | Observar junto con RSS |
| Memoria de pila de Go | `go_memstats_stack_inuse_bytes` | Uso de pila de goroutine | Bytes | Aumenta junto con el crecimiento de las goroutines |
| Node.js Conteo de GC | `nodejs_gc_duration_seconds_count` | Node.js Conteo de GC | veces/seg | GC frecuente puede indicar presión en el heap |
| Node.js Duración del GC | `nodejs_gc_duration_seconds_sum` | Node.js Duración total del GC | seg/seg | El aumento en la duración del GC puede afectar la respuesta |
| Node.js Espacio de heap | `nodejs_heap_space_size_used_bytes` | Uso de cada uno Node.js espacio de heap | Bytes | Se necesita atención si se acerca al límite o crece continuamente |
| Proceso CPU | `process_cpu_seconds_total` | Proceso CPU tiempo | núcleos/seg | Alto CPU uso |
| Proceso RSS | `process_resident_memory_bytes` | Memoria física del proceso | Bytes | Crecimiento RSS continuo |
| Manejadores del proceso | `process_open_fds` | Número de descriptores de archivo abiertos en el proceso | recuento | Fugas de manejadores, fugas de conexión |

Sugerencia de investigación: métricas de runtime de Go y Node.js se usan principalmente para explicar la latencia de la aplicación y el aumento de recursos. Cuando la aplicación P95 aumenta, si la duración del GC aumenta simultáneamente, priorizar la revisión de la asignación de memoria y el ciclo de vida de los objetos.

### 10.4 Servicio de Edición Colaborativa

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Unidades Comunes | Indicaciones Anormales |
| --- | --- | --- | --- | --- |
| Kafka Retraso del Consumidor | `kafka_consumergroup_lag` | Acumulación de Grupos de Consumidores relacionados en la edición colaborativa | Conteo | El aumento del retraso puede causar demoras en el procesamiento de eventos |
| Duración del Proceso | `process_flow_duration_seconds_bucket` | Duración del proceso de edición colaborativa | P50/P95/P99 | Ralentización en el enlace de colaboración del documento |
| Cantidad de Procesos | `process_total` | Número total de procesos manejados | Veces/Segundo | Cambios anormales en el volumen de procesamiento |
| Tamaño del Contenido del Archivo | `file_content_size_bytes_bucket` | Distribución de tamaños del contenido de archivos | Estadísticas Agrupadas | El aumento en la proporción de archivos grandes puede afectar el tiempo de procesamiento |
| Duración del Conjunto de Cambios | `handle_changeset_cost_seconds_bucket` | Tiempo empleado en procesar el conjunto de cambios | P50/P95/P99 | Aumento en el retraso de sincronización de edición |
| Cantidad de Cómputos de Modoc | `modocComputeCount` | Número de cálculos modoc | Veces/Segundo | Aumento anormal en el volumen de cálculos |
| Invocaciones sin servidor | `serverless_invocations` | Número de llamadas sin servidor | Veces/Segundo | Las fallas o picos en la invocación pueden afectar el enlace |

Consultas comunes:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

Sugerencias de investigación: Para enlaces de edición colaborativa, Kafka Se deben examinar juntos el retraso, la duración del proceso, la duración del conjunto de cambios y el tamaño del archivo. Cuando aumenta la proporción de archivos grandes, un aumento en la duración podría ser presión normal de capacidad en lugar de una falla puntual.

### Servicio RS 10.5

| Dimensión de Monitoreo | Métrica | Significado de la Métrica | Alcance/Unidad común | Rendimiento Anormal |
| --- | --- | --- | --- | --- |
| HTTP Conteo de Solicitudes | `http_requests_total` | Número acumulativo de HTTP solicitudes | veces/segundo | Aumento o disminución repentina en las solicitudes |
| HTTP Duración | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP Duración de la solicitud | P50/P95/P99 | Latencia de interfaz aumentada |
| gRPC Conteo de Solicitudes | `grpc_requests_total` | Número acumulativo de gRPC solicitudes | veces/segundo | gRPC Excepciones de llamadas |
| gRPC Duración | `grpc_requests_duration_seconds` | gRPC Duración de la solicitud | P50/P95/P99 | Procesamiento downstream o interno más lento |
| Duración de la tarea de exportación | `export_task_duration_milliseconds_count` | Número y duración del procesamiento de tareas de exportación | ms/tiempo | Tareas de exportación ralentizándose o acumulándose |
| Duración de la tarea de importación | `import_task_duration_milliseconds_count` | Número de procesos de tareas de importación y duración | ms / por tarea | Tareas de importación ralentizadas o apiladas |
| Tareas de exportación en progreso | `export_task_in_progress` | Tareas de exportación actualmente en ejecución | recuento | Si no disminuye durante mucho tiempo, indica que las tareas están atascadas |
| Tareas de importación en progreso | `import_task_in_progress` | Tareas de importación actualmente en ejecución | recuento | Si no disminuye durante mucho tiempo, indica que las tareas están atascadas |
| Métricas de Tokio | `tokio_metrics` | Métricas del runtime Tokio de Rust | valor/ tasa actual | Cola de ejecución o programación de tareas anormal |
| Métricas de jemalloc | `jemalloc` | Métricas del asignador de memoria | Bytes / contador | Fragmentación de memoria o anomalía de asignación |
| TCP Métricas | `tcp` | Servicio RS TCP Métricas relacionadas con la conexión | conteo / tasa | Presión de la conexión o anomalía de red |

Sugerencia de investigación: El servicio RS debería examinar tanto las solicitudes en línea como las tareas de larga duración, como importación/exportación. Un número continuamente no decreciente de tareas en progreso generalmente indica que 'las tareas están atascadas' más confiablemente que la duración promedio.

## 11. Lectura de métricas y sugerencias de investigación

### 11.1 Orden general de investigación

| Paso | Elemento de observación | Propósito |
| --- | --- | --- |
| 1 | `up`, hora de inicio, Pod Listo | Confirme si el servicio todavía está activo y si se ha reiniciado recientemente |
| 2 | Volumen de solicitudes, tasa de errores, P95/P99 latencia | Determine si realmente afecta al negocio |
| 3 | CPU, memoria, disco, red | Determine si hay un cuello de botella de recursos |
| 4 | Latencia de dependencias aguas abajo, Kafka Retraso, consultas lentas a la base de datos | Determine si se ralentiza por dependencias |
| 5 | Versión de lanzamiento, configuración, cambios de tráfico | Determine si está relacionado con cambios |

Al solucionar problemas realmente, no se apresure a ver todos los gráficos primero. Primero, confirme "si hay un impacto en el negocio", luego encuentre "de dónde proviene el impacto". Por ejemplo, si una interfaz es lenta, primero observe la P95de la aplicación, luego revise la latencia de la dependencia del cliente; si la dependencia es normal, revise nuevamente la CPU, recolección de basura (GC), memoria y limitación del contenedor.

### 11.2 Combinaciones comunes de excepciones

| Síntoma | Rendimiento común de métricas | Dirección de investigación prioritaria |
| --- | --- | --- |
| Interfaz lenta | Aplicación P95/P99 en aumento, CPU no alto | Dependencias aguas abajo, consultas lentas a la base de datos, Kafka Retraso |
| CPU totalmente utilizado | `container_cpu_usage_seconds_total` alto, limitación alta | CPU límites, interfaces calientes, tareas de procesamiento por lotes |
| Memoria OOM | Conjunto de trabajo cerca del límite, aumento del recuento de reinicios | Fugas de memoria, límite demasiado pequeño, procesamiento de objetos grandes |
| Disco lento | iowait, IO ocupado, aumento de la latencia de lectura/escritura | Base de datos, Kafka, MinIO, escritura de registros |
| Red anormal | Aumento del tráfico acompañado de caída/errores | Nodo NIC, CNI, enlace, número de conexiones |
| Kafka Latencia | `kafka_consumergroup_lag` Aumento continuo | Instancias de consumidores, tiempo de consumo, dependencias aguas abajo |
| Redis Contrapresión | Tasa de aciertos disminuyendo, fallos aumentando | Política de expiración de claves, penetración de caché, capacidad |
| MySQL Lento | consultas lentas, escaneo, aumento de espera por bloqueo | SQL, índices, bloqueos, E/S de disco |
| MinIO Riesgo | Disco fuera de línea, tasa de errores, niveles de capacidad en aumento | Disco, nodos, crecimiento del bucket, estado de recuperación |
| Elasticsearch Consulta lenta | tiempo de búsqueda/recuperación de consultas en aumento, rechazos del grupo de hilos en aumento | Condiciones de consulta, estructura de índices, JVM, E/S de disco |
| Elasticsearch Escritura lenta | tiempo de indexación, tiempo de fusión, aumento de limitación de almacenamiento | Picos de escritura, actualización, fusión, niveles de disco |
