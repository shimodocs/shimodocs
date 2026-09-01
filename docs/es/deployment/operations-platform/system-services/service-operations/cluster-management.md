# Gestión de Clúster

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Descripción General de la Función

El módulo de Gestión de Clústeres es una consola en la MDP Plataforma de operaciones que se conecta con el cliente Kubernetes clústeres, apuntando a tres escenarios: inspecciones diarias, solución de problemas de emergencia y cambios de recursos. El objetivo de este módulo es permitir que el personal de operaciones en servicio complete tareas comunes de solución de problemas y operaciones sin cambiar frecuentemente al nativo `kubectl`.

Capacidades principales:

- Resumen del clúster: estado de los nodos, estado de ejecución de las aplicaciones
- Gestión de cargas de trabajo: ver, reiniciar, cambiar el número de réplicas, modificar recursos de contenedores y ver YAML para Deployment, StatefulSet, DaemonSet, Pod, Job y CronJob
- Gestión de Configuración: visualización de ConfigMap, HorizontalPodAutoscaler (HPA)
- Recursos de Red: visualización de Service, Ingress
- Diagnóstico a nivel de Pod: registros en tiempo real, registros de fallos, K8s eventos, terminal web, YAML ver

### 1.1 Usuarios Aplicables

| Rol        | Operaciones Comunes                                      |
| ----------- | ---------------------------------------------------- |
| Operaciones en turno | Visualización de anomalías de nodos y Pods, consulta de registros, visualización de eventos |
| Soporte en sitio | Ver estado de réplicas de Deployment, versión de imagen, Solicitud/Límite de recursos         |
| Emergencia por falla | Reiniciar Deployment o DaemonSet, ajustar número de réplicas, ajustar CPU/Memoria |
| Planificación de capacidad | Ver HPA número actual de réplicas y límites superior/inferior                             |

### 1.2 Operaciones no recomendadas en este módulo

Eliminar NAMESPACE, expulsar Pods a la fuerza, modificar Secret o RBAC recursos, y otras operaciones sensibles no están disponibles en este módulo y deben ejecutarse mediante herramientas nativas `kubectl` o herramientas de cambio relevantes. Las operaciones por lotes entre clústeres no están disponibles; cada operación solo afecta al clúster actualmente seleccionado y NAMESPACE. Para descargar registros de archivos grandes a la vez, se recomienda usar la terminal web en lugar de una ventana emergente de registro en streaming.

---

## 2. Entrada y Navegación

Menú izquierdo: **Gestión de Operaciones → Gestión de Clúster**.

Después de ingresar, el **menu Despliegues** en el lado izquierdo se selecciona por defecto. El NAMESPACE se establece en el primero en el clúster actual, y se admite la selección personalizada del clúster y NAMESPACE .

---

## 3. Cargas de trabajo

### 3.1 Despliegues
**Pasos**: Encontrar el Despliegue objetivo → Hacer clic en el icono del lápiz en la parte superior derecha → Aparece la ventana emergente de edición → Ingresar nuevos valores → Confirmar cambios.

Campos admitidos para modificación en la ventana emergente: 

- Número de réplicas, valor mínimo 0, debe ser un entero 
- CPU Solicitud / Límite por contenedor, la unidad es "core", puede rellenarse `1` o `1000m` 
- Solicitud / Límite de memoria por contenedor, la unidad es Mi, puede rellenarse `512` 

Después de enviar, se inicia una reconstrucción progresiva. Los campos que no se enumeran (imagen, variables de entorno, sondas, etc.) no se cambiarán. 

#### 3.1.1 Reinicio de Despliegue 
Pasos: Encontrar el Despliegue objetivo → Hacer clic en el icono de flecha circular en la parte superior derecha → Confirmar que aparece la ventana emergente → Verificar clúster / NAMESPACE / nombre de carga → Confirmar reinicio. 

La ventana emergente de confirmación indica claramente que "reiniciar causará que los Pods se reconstruyan y los servicios pueden interrumpirse brevemente." Reiniciar reconstruirá los Pods en todos los nodos simultáneamente. 

### 3.2 Pods
**Pasos de operación**: Ir a Pods desde el menú izquierdo → La sección inferior lista todos los Pods bajo el actual NAMESPACE, admitiendo búsqueda por Namespace, POD_NAME, IP del Pod, y NODE_IP. 

Esto YAML es solo para visualización.

### 3.3 Trabajos y CronJobs

#### Trabajos
**Pasos**: Vaya a Trabajos desde el menú de la izquierda → La tabla muestra todos los Trabajos en el actual NAMESPACE.

Se puede buscar por Espacios de nombres y Nombre.

#### CronJobs
**Pasos**: Vaya a CronJobs desde el menú de la izquierda → La tabla muestra todos los CronJobs bajo el actual NAMESPACE.

Se puede buscar por Espacios de nombres y Nombre. 
Haga clic **** **** para expandir y mostrar la subtabla de Pods correspondientes a todos los Trabajos activados por este CronJob. 

### 3.4 DaemonSets 
**Pasos de operación**: Ingrese a DaemonSets desde el menú de la izquierda. 

Se puede buscar por Espacios de nombres y nombre de carga de trabajo.
Operaciones compatibles:

- Modificar: CPU / La memoria se puede cambiar, el número de réplicas no se puede cambiar.
- Reiniciar: Reconstruir Pods en todos los nodos simultáneamente.
- YAML: Solo ver.

### 3.5 StatefulSets
**Pasos de operación**: Vaya a StatefulSets desde el menú de la izquierda → Vista de tabla.

Modificar el número de réplicas, CPU/memoria, reinicios o listas de Pods de StatefulSets no es compatible. Los cambios requeridos deben realizarse usando el nativo `kubectl` (ver Apéndice B).

---

## 4. Configuración

### 4.1 ConfigMaps
**Pasos**: Ingrese a ConfigMaps desde el menú de la izquierda → la tabla muestra todos los ConfigMaps en el actual NAMESPACE.
[Gestión del Clúster] no admite edición de clave-valor. Para cambios, por favor vaya al Centro de Configuración.

### 4.2 HPA
**Pasos de operación**: Ingrese HPA desde el menú de la izquierda → La tabla muestra todos los HPAs bajo el actual NAMESPACE.

Solo ver. Para modificar el HPA mínimo y máximo, por favor use el nativo `kubectl`.

---

## 5. Red

### 5.1 Servicios
**Pasos**: Use el menú de la izquierda para ir a Servicios → una tabla muestra todos los Servicios bajo el actual NAMESPACE.

Solo ver. Para realizar cambios, por favor modifíquelos a través de la configuración global mdp. 

### 5.2 Ingresses
**Pasos**: Ve a Ingresses desde el menú de la izquierda → La tabla lista todos los Ingresses bajo la vista actual NAMESPACE. 

Solo vista, para hacer cambios por favor modifica a través de la configuración global de mdp.

---

## 6. Operaciones Comunes

### 6.1 Solución de Problemas de Pods

1. Usa el menú desplegable superior para cambiar al clúster correspondiente y NAMESPACE
2. Ve a Pods en el menú de la izquierda
3. Filtrar por POD_NAME o IP
4. Presta atención al campo Fase en la parte superior de la tarjeta, prioriza `Failed` y `Pending`
5. La Condición correspondiente al indicador de salud atenuado es el punto del problema
6. Haz clic en el ícono de "Eventos" al final de la fila para encontrar la causa raíz
7. Usa "Logs" para ver la salida en tiempo real / "Crash Logs" para ver la última salida del contenedor

### 6.2 Reiniciar Deployment

1. Ve a Deployments en el menú de la izquierda
2. Encuentra el Deployment objetivo
3. Haz clic en el ícono de flecha circular en la parte superior derecha
4. Confirma la ventana emergente verificando el clúster / NAMESPACE / nombre del workload → confirma el reinicio
5. Observa la barra de progreso del estado de réplicas de los Pods en la parte inferior de la tarjeta para juzgar el progreso de reconstrucción

### 6.3 Reducir Réplicas de Deployment para Verificación

1. Entra en el Deployment correspondiente
2. Haz clic en el ícono de lápiz "Editar"
3. Ingresa el nuevo valor para el número de réplicas (puede configurarse a 0 para depuración) 
4. Ajusta CPU / Memoria según sea necesario (opcional) 
5. Confirma los cambios y espera la actualización continua 

Antes de reducir el número de réplicas, se recomienda confirmar con SRE los colegas si el valor objetivo afectará el tráfico en línea. 

### 6.4 Modificar ConfigMap 

La plataforma no soporta la edición de pares clave-valor de ConfigMap en Gestión del Clúster - Configuración - ConfigMap. Por favor, ve al Centro de Configuración. 

--- 

## 9. Preguntas Frecuentes 

**P1: La vista general superior muestra que la tasa de funcionamiento de la aplicación no es del 100%.** 

Esto significa que hay Pods bajo el actual NAMESPACE que no están en estado Ready (incluyendo Pending, CrashLoopBackOff, Error, etc.). Por favor, vaya al menú de Pods a la izquierda, filtre por POD_NAME o IP, y revise los eventos y registros de cada Pod que no esté Ready. 

**Q2: El popup está en blanco después de hacer clic en 'Modificar Deployment'.** 

Hay tres razones comunes: la fluctuación de la red, demasiadas `managedFields` en el objeto de recurso, o servidor API excepciones. Por favor, deshabilite primero la reintento; si todavía está en blanco, contacte SRE y proporcione el nombre del clúster / espacio de nombres / carga de trabajo para la resolución de problemas. 

**P3: El YAML contenido emergente es muy grande.** 

Fenómeno normal. K8s Los objetos de recursos por defecto llevan muchos metadatos y condiciones, con el contenido clave concentrado en la `spec` sección. 

**P4: Sin salida en la ventana emergente de registro.** 

El contenedor puede que no esté enviando registros a stdout/stderr, por favor revise la política de salida de registros de la aplicación. Si el contenedor se ha bloqueado, use el icono "Registro de fallos" para obtener la salida de la instancia anterior. 

**P5: La modificación del número de réplicas o de los recursos no surtió efecto.** 

La plataforma emite un Parche de Fusión Estratégica, y K8s entrará en el proceso de reconciliación dentro de unos segundos. Si no hay cambios en 30 segundos, por favor vuelva al nativo `kubectl describe deployment` para revisar los eventos. 

**P6: No se pueden modificar StatefulSets, ConfigMaps, HPA, Servicios, Ingresses.** 

La plataforma solo permite ver estos recursos. Las modificaciones deben realizarse a través de la configuración global de mdp, y solo se admiten Servicios e Ingresses. 

--- 

--- 

## Apéndice A: Clave kubectl comandos utilizados en esta plataforma 

Los siguientes comandos se usan para ejecutarse directamente en el host o terminal de mantenimiento como una vía alternativa cuando las funciones de este módulo no están cubiertas. 

```bash
# View
kubectl get  statefulset <name> -n <ns>
kubectl get deployment <name> -n <ns>

# Restart STS / deployment
kubectl rollout restart statefulset/<name> -n <ns>
kubectl rollout restart deployment/<name> -n <ns>

# View the complete Ingress rule chain
kubectl describe ingress <name> -n <ns>
```

`kubectl describe deployment <name> -n <ns>` pueden usarse para solucionar problemas del progreso de la conciliación emitida por la plataforma después de la modificación.

Precauciones:
Se debe evitar modificar recursos administrados por MDP como deployment, configmap, ingress, sts, etc., a través de kubectl . La forma correcta de operar es usar la MDP configuración del backend.

## Apéndice B: Glosario de Términos

| Término               | Explicación                                           |
| --------------- | ---------------------------------------------------- |
| Cluster         | Objetivo K8s cluster, configurado y emitido cuando MDP inicia                              |
| Namespace       | K8s NAMESPACE, usado para aislamiento de negocio o entorno                                   |
| Workload        | Workload, generalmente se refiere a Deployment, StatefulSet, DaemonSet, Job, CronJob |
| Pod             | La unidad de programación más pequeña en K8s, llevando de 1 a N contenedores                              |
| HPA             | HorizontalPodAutoscaler, escalado horizontal basado en métricas                  |
| Request / Limit | Reserva / límite de recursos del contenedor, la plataforma soporta modificar ambos |
| Patch           | Actualización parcial, esta plataforma usa Strategic Merge Patch                     |
| STS             | Abreviatura de StatefulSet                                       |
| DS              | Abreviatura de DaemonSet                                         |
