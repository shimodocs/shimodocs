# Planificación de Recursos

[← ShimoDocs Suite Documentación de implementación](../README.md)

## 1. Propósito del Documento

Este documento se utiliza para guiar la planificación de recursos de servidor y middleware en escenarios de despliegue privatizado, como referencia para ingenieros de implementación, ingenieros de operaciones y personal de soporte técnico de preventa.

El contenido del documento se basa en la planificación de capacidad de proyectos históricos, configuraciones de muestra y bases del middleware, y puede ser utilizado para estimación de preventa, solicitud de recursos, despliegue de implementación y evaluación de expansión posterior.

## 2. Alcance e Instrucciones

### 2.1 Alcance

Este documento se aplica a la planificación preliminar de nodos de aplicación y recursos de middleware para diferentes escalas de usuarios en escenarios de despliegue privatizado.

### 2.2 Instrucciones

* Las configuraciones en este documento son todas configuraciones recomendadas, utilizadas para la evaluación de capacidad de proyectos en etapas tempranas y la planificación de recursos.

* Los recursos de los nodos de la aplicación y los recursos de middleware deben calcularse por separado; no se recomienda la planificación mixta.

* En escenarios de usuarios a gran escala, los recursos de middleware necesitan ser calibrados adicionalmente según la carga máxima del negocio, los modelos de concurrencia, los resultados de pruebas de estrés de capacidad y los datos de monitoreo de producción.

* En un entorno de producción formal, se recomienda reservar capacidad de expansión y priorizar la construcción de alta disponibilidad.

* Si se utilizan servidores de arquitectura nacionales, CPU se recomienda estimar los recursos totales como el doble de la especificación estándar.

## 3. Principios de Planificación

### 3.1 Principios de Despliegue de Aplicaciones y Middleware

* Para escenarios con menos de 10,000 usuarios, es posible evaluar si desplegar parte del middleware dentro del K8s cluster según la situación real del proyecto.

* Para escenarios con 10,000 o más usuarios, se recomienda desplegar los nodos de aplicación y el middleware completamente por separado.

* Se recomienda desplegar middleware central como bases de datos, caches, colas de mensajes y servicios de búsqueda con arquitectura de alta disponibilidad como prioridad.

* Cuando las condiciones lo permitan, se recomienda priorizar el uso de servicios maduros de middleware gestionado en la nube pública para mejorar la estabilidad y la mantenibilidad.

### 3.2 Principios de Planificación de Almacenamiento de Objetos

* Preferiblemente utilizar servicios de almacenamiento de objetos en la nube pública, como Alibaba Cloud OSS, Huawei Cloud OBS, Tencent Cloud COS, AWS S3.

* Si se utiliza despliegue privado de almacenamiento de objetos, SSD se deben usar discos, y el rendimiento, la estabilidad y la operatividad después del crecimiento de capacidad deben evaluarse cuidadosamente.

* Si el negocio implica grandes cantidades de cargas, descargas, vistas previas de archivos grandes o escenarios de edición colaborativa de hojas de cálculo grandes por múltiples usuarios, se recomienda priorizar el uso de servicios de almacenamiento de objetos independientes.

## 4. Planificación de Nodos de Aplicación

### 4.1 Clasificación de Especificaciones de Nodos de Aplicación

#### Especificación A

* Especificación recomendada: `24C / 48G / >=500G SSD * N`

* Rango aplicable: menos de 10,000 usuarios

* Funciones aplicables: 

   * Puede soportar escenarios de pequeñas y medianas empresas 

   * El middleware se puede desplegar en el K8s entorno dependiendo del proyecto 

   * Un solo nodo soporta una carga alta; cuando un nodo falla, el rango de impacto es relativamente grande 

#### Especificación B 

* Especificación recomendada: `16C / 32G / >=300G SSD * N` 

* Rango aplicable: 10,000 usuarios y más 

* Funciones aplicables: 

   * Adecuado para escenarios de implementación a gran escala y alta disponibilidad 

   * Debe usar middleware independiente 

   * Usa un enfoque de múltiples nodos de pequeña especificación, proporcionando una distribución más equilibrada y una escalabilidad más flexible 

   * Cuando un nodo se mantiene o tiene un problema, el impacto general en el negocio es menor 






### 4.2 Criterios de Cálculo de Nodos de Aplicación 

Basado en ejemplos de proyectos existentes y reglas de cálculo de capacidad, se recomienda estimar los nodos de aplicación usando la siguiente fórmula: 

`Number of nodes = Number of users × 0.03 ÷ 160` 

Se puede entender simplemente como: 

`Number of nodes ≈ Number of users ÷ 5300`

Donde:

* El coeficiente de usuario concurrente se estima en `0.03`.

* La capacidad de un solo `16C / 32G` nodo es aproximadamente `150 ~ 180 QPS`.

* Se recomienda usar `160 QPS/node` como base de cálculo.

* Se recomienda redondear hacia arriba el resultado calculado, reservando capacidad adicional para la expansión.

### 4.3 Tabla de Configuración Recomendada para Nodos de Aplicación

| Escala de Usuarios (Personas) | Especificaciones del Nodo | Cantidad Recomendada | Sugerencias de Despliegue |
|:----|:----|:----|:----|
|500|24C / 48G / 500G SSD|1 unidad|Puede desplegarse en una sola máquina; para alta disponibilidad, se recomienda desplegar al menos 3 servidores|
|3000|24C / 48G / 500G SSD|3 unidades|Modo clúster, despliegue de alta disponibilidad (umbral mínimo de especificación para despliegue en clúster)|
|10,000|24C / 48G / 500G SSD|3 unidades|Modo clúster, despliegue de alta disponibilidad; el uso de middleware externo puede evaluarse según las necesidades del proyecto|
|30,000|16C / 32G / 300G SSD|5 unidades|Modo clúster, despliegue de alta disponibilidad, usando middleware independiente|
|50,000|16C / 32G / 300G SSD|10 unidades|Modo clúster, despliegue de alta disponibilidad, usando middleware independiente|
|100,000|16C / 32G / 300G SSD|18 ~ 20 unidades|Se recomienda comenzar con 18 unidades y reservar capacidad para expansión, usando middleware independiente|
|200,000|16C / 32G / 300G SSD|38 ~ 40 unidades|Se recomienda construir y desplegar por fases|
|300,000|16C / 32G / 300G SSD|56 ~ 60 unidades|Se recomienda construir y desplegar por fases|
|500,000|16C / 32G / 300G SSD|94 ~ 100 unidades|Se recomienda planificar un pool de recursos independiente y construir y desplegar por fases|
|700,000|16C / 32G / 300G SSD|132 ~ 140 unidades|Se recomienda planificar un pool de recursos independiente y construir y desplegar por fases|

### 4.4 Conclusiones de Planificación de Nodos de Aplicación

* Se recomienda que los usuarios por debajo de 10,000 usen la Especificación A.

* Se recomienda que los usuarios de 10,000 en adelante usen la Especificación B.

* Para una escala de usuarios de 100,000, puede comenzar con 18 unidades según el ancla de ejemplo, y otras escalas se estiman según una fórmula unificada y se redondean hacia arriba.

* Para proyectos en crecimiento continuo, se recomienda proceder con una estrategia de expansión por fases para evitar una inversión excesiva de una sola vez.

## 5. Planificación de Middleware

### 5.1 Principios de Clasificación del Middleware

La planificación actual de recursos de middleware se ejecuta de acuerdo con dos niveles base:

* `Users below 3,000`: Usar configuración base a pequeña escala.

* `3000 users and above`: Usar configuración base a gran escala. 

Para escenarios de mayor escala como 10,000, 30,000, 50,000, 100,000, 200,000, 300,000, 500,000, 700,000 usuarios, se recomienda comenzar uniformemente con la configuración base de '3000 usuarios y más', y escalar dinámicamente según el crecimiento del negocio. 

### 5.2 Tabla de Especificaciones de Middleware 

|Middleware|Versión Recomendada|Menos de 3000 Usuarios|3000 Usuarios y Más|Requisitos de Alta Disponibilidad| 
|:----|:----|:----|:----|:----| 
|MySQL|MySQL 8.0|4C / 8G / 200G SSD|8C / 16G / 200G SSD|Alta disponibilidad con conmutación por fallo maestro-esclavo<br>Conjunto de caracteres: utf8mb4<br>Zona horaria: Asia/Shanghai o UTC<br>Conexiones: máximo_conexiones ≥ 1000| 
|MongoDB|MongoDB 4.4|2C / 8G / 100G SSD|4C / 16G / 100G SSD|Alta disponibilidad de clúster de conjunto de réplicas| 
|Redis|Redis 6.2.21|2C / 4G / 100G SSD|2C / 8G / 100G SSD|Alta disponibilidad maestro-esclavo / sentinel, persistencia de datos; Modo clúster no soportado; Número de DBs ≥ 64| 
|Kafka|Kafka 3.5|2C / 4G / 300G SSD|4C / 8G / 300G SSD|brokers >= 3, factor de replicación predeterminado 3<br>Retención de mensajes: 72 horas (ajustable según necesidades del negocio)<br>Tamaño máximo de mensaje único por Topic: 10 MB<br>Autenticación: Soporta SASL acceso cifrado (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512)|
|Elasticsearch|ES 8.18.5|2C / 4G / 200G SSD|4C / 8G / 200G SSD|Número de nodos >= 3<br>Instalaciones requeridas:<br>analysis-ik (segmentación de palabras en chino),<br>analysis-pinyin (segmentación Pinyin)|
|Almacenamiento de Objetos|S3 protocolo compatible|Compatible con S3|Compatible con S3 protocolo|Prefiere nube pública, debe soportar HTTPS acceso externo|

Nota: 

* Las especificaciones de middleware anteriores deben escalarse según la carga real





## 6. Sugerencias de Implementación y Operación & Mantenimiento

### 6.1 Sugerencias de Implementación de Despliegue

* MySQL, MongoDB, Redis, Kafka, Elasticsearch se recomienda desplegar en modo de clúster de alta disponibilidad.

* Si las condiciones lo permiten, se recomienda dar prioridad al uso de bases de datos gestionadas y servicios de middleware en la nube pública para mejorar la estabilidad y la mantenibilidad.

* Para escenarios de usuarios con 10,000 o más usuarios, se recomienda desplegar los nodos de la aplicación y el middleware por separado. 

* Para Kafka, se recomienda usar una instancia separada para evitar compartir recursos con otros negocios. 

### 6.2 Recomendaciones de Implementación de Almacenamiento de Objetos 

* Se recomienda dar prioridad al uso de productos de almacenamiento de objetos en la nube pública. 

* Si se utiliza almacenamiento de objetos privado, SSD deben emplearse discos. 

* Si el espacio del equipo involucra un gran número de escenarios para cargar, descargar o previsualizar archivos grandes, la capacidad de almacenamiento de objetos, el rendimiento y el ancho de banda deben ser factores clave de evaluación. 

### 6.3 Consideraciones de Escalado 

En los siguientes escenarios de negocio, se recomienda dar prioridad a evaluar y agregar recursos de middleware: 

* Gran cantidad de archivos adjuntos que se suben, descargan o previsualizan 

* Búsqueda de texto completo de alta frecuencia 

* Acumulación de mensajes o tareas asincrónicas intensivas 

* Escrituras por lotes y análisis estadístico durante periodos pico 

* Crecimiento continuo en el volumen de registros 

Las métricas clave en las que centrarse incluyen: 

* Base de datos: CPU, memoria, IO de disco 

* Redis: número de conexiones, tasa de aciertos, uso de ancho de banda 

* Kafka: número de brokers, acumulación de mensajes, espacio en disco 

* Elasticsearch: Número de nodos, tamaño del índice, capacidad de almacenamiento 

* Almacenamiento de objetos: rendimiento de lectura/escritura, rendimiento de solicitudes, capacidad, ancho de banda 






## 7. Conclusión 

* Para escenarios a pequeña escala (usuarios inferiores a 10,000), se recomienda utilizar configuraciones de nodo de aplicación de la Especificación A, y evaluar si se debe desplegar algún middleware dentro del clúster según la situación del proyecto. 

* Para escenarios de mediana y gran escala (10,000 usuarios o más), se recomienda utilizar la configuración de nodo de aplicación de la Especificación B, junto con middleware independiente y una arquitectura de alta disponibilidad. 
* Se recomienda configurar el middleware basado en dos líneas base: "menos de 3000 usuarios" y "3000 o más". Para proyectos a gran escala, la expansión continua se basa en pruebas de estrés y datos de monitoreo. 
* Antes de la implementación oficial, se debe completar simultáneamente la confirmación de la planificación de recursos, la verificación de compatibilidad y las pruebas de estrés de capacidad para evitar discrepancias entre las especificaciones de despliegue y el alcance realmente soportado. 

* Si se utiliza un proveedor nacional CPU servidor de arquitectura, se recomienda estimar los recursos al doble de la especificación estándar. 

* Este manual es para la selección previa a la instalación y no reemplaza las pruebas de estrés in situ ni la implementación final.
