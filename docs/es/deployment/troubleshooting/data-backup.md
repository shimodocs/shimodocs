# Copia de Seguridad de Datos

[← ShimoDocs Suite Documentación de implementación](../README.md)

Este documento explica el alcance de la copia de seguridad de datos, los requisitos de recuperación, los métodos de ejecución y los elementos de verificación posteriores a la recuperación para el ShimoDocs entorno privatizado.

Este documento cubre los siguientes contenidos:

* Alcance de la copia de seguridad y límites de responsabilidad

* Requisitos de copia de seguridad y recuperación de bases de datos

* Requisitos de copia de seguridad y recuperación de almacenamiento de objetos

* Elementos de confirmación previos a la recuperación

* Elementos de verificación posteriores a la recuperación

Este documento no cubre los siguientes contenidos:

* Pasos de instalación y despliegue inicial

* Planes de actualización y migración

* Instrucciones de herramientas de recuperación específicas de proveedores de middleware de terceros

* Procesos de manejo de incidentes de producción

# 2. Alcance de la copia de seguridad y límites de responsabilidad

## 2.1 Alcance de la copia de seguridad

Los datos que deben incluirse en el alcance de la copia de seguridad en el ShimoDocs entorno privatizado incluyen:

* MySQL datos

* MongoDB datos

* Redis datos

* Datos de almacenamiento de objetos 

* Archivos de configuración de instalación y parámetros del entorno 

Los directorios de datos, los directorios de copia de seguridad y los períodos de retención de copia de seguridad se gestionan de manera uniforme por el lado del cliente. 

## 2.2 Límites de responsabilidad 

Los límites de responsabilidad de la copia de seguridad y recuperación son los siguientes: 

* El lado del cliente es responsable de formular y ejecutar políticas de copia de seguridad formales 

* El lado del cliente es responsable de la custodia de los archivos de copia de seguridad, la seguridad de los medios y la gestión del período de retención 

* El lado del cliente es responsable de los simulacros de recuperación, la aprobación de la recuperación y la aceptación de los resultados de la recuperación 

* ShimoDocs puede proporcionar soporte técnico y orientación en operaciones de recuperación 

Cuando se utiliza middleware externo, almacenamiento de objetos auto-construido o infraestructura mantenida por el cliente, la estrategia de respaldo y recuperación es totalmente realizada por el lado del cliente. 

# 3. Confirmación antes de la ejecución de la recuperación 

La recuperación de datos es una operación de alto riesgo. Las siguientes confirmaciones deben completarse antes de la ejecución. 

## 3.1 Confirmación del objetivo 

Antes de la recuperación, aclare la siguiente información: 

* Entorno objetivo 

* Clúster objetivo, nodos, NAMESPACE 

* Alcance de los datos a recuperar 

* Punto de recuperación en el tiempo 

* Ventana de ejecución 

## 3.2 Confirmación de riesgos

Confirme los siguientes elementos antes de la recuperación:

* Si esta recuperación sobrescribirá los datos en línea actuales

* Si esta recuperación requiere tiempo de inactividad

* Si la copia de seguridad más reciente se ha agregado a los datos en línea actuales

* Si se ha aclarado el punto de reversión después de una recuperación fallida

## 3.3 Confirmación de la validez de la copia de seguridad

Verifique lo siguiente antes de la recuperación:

* Los archivos de copia de seguridad están completos y son legibles

* El punto de tiempo de la copia de seguridad cumple con los objetivos de recuperación

* El directorio de copia de seguridad está montado correctamente

* Todos los archivos de configuración necesarios para la recuperación están completos

* Los archivos de copia de seguridad han pasado la verificación de recuperabilidad

# 4. Estrategia de copia de seguridad

## 4.1 Copia de seguridad de la base de datos

Los criterios de la copia de seguridad de la base de datos son los siguientes:

|Escenario|Método de ejecución|Frecuencia|Período de retención|Descripción|
|:----|:----|:----|:----|:----|
|Usando ShimoDocs middleware incorporado|Copia de seguridad programada por el sistema|Una vez al día|7 días|Ejecutado por tareas programadas dentro del clúster|
|Usar middleware mantenido por el cliente|Copia de seguridad del lado del cliente|Una vez al día o más|7 días o más|Ejecutar según la política del cliente|



La copia de seguridad de la base de datos debe cubrir al menos:

* MySQL

* MongoDB

* Redis

## 4.2 Copia de Seguridad de Almacenamiento de Objetos

Los criterios de copia de seguridad de almacenamiento de objetos son los siguientes:

|Tipo de Datos|Método de ejecución|Frecuencia|Período de retención|Descripción|
|:----|:----|:----|:----|:----|
|Datos comerciales de almacenamiento de objetos|Copia de seguridad en frío o replicación para recuperación ante desastres|Ejecutar según el nivel de negocio|Ejecutar según la política del cliente|Cubre documentos adjuntos y objetos de archivo|
|Datos de configuración de almacenamiento de objetos|Copia de seguridad de configuración|Copia de seguridad sincronizada después de los cambios|Ejecutar según la política del cliente|Cubre parámetros de acceso e información de montaje|



Múltiples copias en almacenamiento de objetos son parte del mecanismo de redundancia del clúster y no son equivalentes a la copia de seguridad de datos.

## 4.3 Copia de Seguridad de Archivos de Configuración

Las siguientes configuraciones están incluidas en el alcance de la copia de seguridad:

* Parámetros de instalación

* Configuración de dominio y protocolos

* Direcciones de dependencia externa e información de puertos

* Información de acceso al almacenamiento de objetos 

* Archivos de configuración relacionados con el negocio 

# 5. Recuperación de Base de Datos 

Esta sección se aplica a toda recuperación de datos para MySQL, MongoDB, y Redis. 

## 5.1 Preparación Antes de la Recuperación 

Complete las siguientes preparaciones antes de realizar la recuperación de la base de datos: 

* Prepare un directorio de recuperación en el nodo objetivo, por ejemplo, `/data/restore` 

* Coloque los datos a recuperar en el directorio de recuperación 

* Verifique que la configuración del middleware en el `global_config.json` archivo coincida con el entorno actual 

* Revise el nodo de recuperación, el punto de recuperación, la ventana de ejecución y la información de aprobación 

## 5.2 Comprobación de la Tarea de Copia de Seguridad 

Verifique las tareas de copia de seguridad de la base de datos programadas: 

```plain
kubectl get cronjob
```


También registre la siguiente información:

* Nombre del CronJob

* Última hora de ejecución

* Resultado de la ejecución más reciente

* Directorio de almacenamiento de archivos de respaldo

## 5.3 Reanudación de la Ejecución

La recuperación de la base de datos se realiza a través de un trabajo único, y el script de recuperación se encuentra en la imagen de respaldo.

Los pasos de ejecución son los siguientes:

1. Preparar la lista de tareas de restauración `db-restore.yaml`

2. Modificar `spec.template.spec.nodeName` al nodo donde se encuentra el directorio de recuperación

3. Modificar `hostPath.path` al directorio donde se restauran los datos

4. Ejecutar el `kubectl apply -f db-restore.yaml` comando para realizar la restauración de datos

La lista de tareas de ejemplo es la siguiente:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    job-name: db-restore
  name: db-restore
spec:
  template:
    metadata:
      labels:
        job-name: db-restore
      name: db-restore
    spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - cd /data/pri-init/scripts/backup && sh restore_all.sh
        image: registryo.shimo.im/smbase/backup:co
        imagePullPolicy: Always
        name: db-restore
        volumeMounts:
        - name: db-config
          mountPath: /data/pri-init/scripts/global_config.json
          subPath: global_config.json
        - name: data
          mountPath: /backup
      dnsPolicy: ClusterFirst
      nodeName: master-1
      volumes:
      - name: db-config
        configMap:
          name: init-invoker
          items:
          - key: global_config.json
            path: global_config.json
      - name: data
        hostPath:
          path: /data/restore
      imagePullSecrets:
      - name: ee
      restartPolicy: Never
      schedulerName: default-scheduler
```


## 5.4 Instrucciones de Ejecución

Después de ejecutar la tarea de recuperación de la base de datos, los siguientes datos se revertirán:

* MySQL

* MongoDB

* Redis

Durante el período de recuperación, los datos comerciales pueden ser sobrescritos. Complete los arreglos de cierre y la verificación de datos antes de la ejecución.

# 6. Recuperación de Almacenamiento de Objetos

Esta sección se aplica a la recuperación de almacenamiento de objetos compatible con MinIO y S3.

## 6.1 Métodos de Respaldo

Los métodos de respaldo comunes para el almacenamiento de objetos son los siguientes:

|Método|Escenario Aplicable|Descripción|
|:----|:----|:----|
|Copia sincronizada Rsync|Entorno independiente|Apto para respaldo en frío a nivel de directorio|
|Instantánea de Disco|Entorno independiente|Apto para recuperación rápida en la misma plataforma de almacenamiento|
|`mc mirror`|Entorno independiente o en clúster|Apto para respaldo y recuperación en frío de datos de objetos|
|Replicación de Sitio / Replicación de Bucket|Entorno en clúster|Apto para replicación de recuperación ante desastres|



## 6.2 Reanudación de la Ejecución

Los métodos de recuperación comúnmente usados en un entorno independiente son los siguientes:

* Al usar Rsync para la copia de seguridad, realice la sincronización inversa para restaurar el directorio de datos

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* Al usar `mc mirror` para la copia de seguridad, realice una restauración espejo inversa

```plain
mc mirror backup-minio/ new-minio/
```


Las pautas de recuperación para el entorno del clúster son las siguientes:

* Cuando exista una copia de recuperación ante desastres, realice la recuperación según el plan de conmutación primaria-standby

* Al usar copia de seguridad en frío, realice la recuperación según el directorio de datos del almacenamiento de objetos o el contenido del repositorio de imágenes

## 6.3 Instrucciones de ejecución

Antes de restaurar el almacenamiento de objetos, se deben confirmar los siguientes asuntos:

* Rango del bucket de destino para la restauración

* Punto de recuperación

* Si se sobrescribe el objeto en línea

* Ruta de almacenamiento de destino y configuración de permisos

* ACCESS_DOMAIN y configuración de la puerta de enlace después de la recuperación

# 7. Verificación Después de la Recuperación

Después de completar la recuperación, como mínimo verifique lo siguiente:

* El estado del servicio de base de datos es normal

* El estado del servicio de almacenamiento de objetos es normal

* Se puede manejar a través del panel de administración

* El inicio de sesión de usuario es normal

* Los documentos principales se pueden crear, editar, guardar, importar y exportar normalmente

* El punto de recuperación de datos es consistente con lo esperado

Registre la siguiente información después de completar la recuperación:

* Tiempo de ejecución de la restauración

* Punto de tiempo de recuperación de datos

* Ejecutor, Aprobador, Inspector

* Problemas encontrados después de la recuperación
