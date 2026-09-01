# Configuración de Middleware

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Visión general 

La configuración de middleware es la página en la MDP Plataforma de Operaciones que se integra con varios sistemas de almacenamiento y middleware en el entorno del cliente, gestionando centralmente la información de conexión para componentes como S3 almacenamiento de objetos, Redis, cola de mensajes Kafka, base de datos relacional MySQL, base de datos documental MongoDB, y búsqueda de texto completo Elasticsearch. Los cambios de configuración se emiten al entorno del cliente a través de tareas asincrónicas, con visualización en tiempo real del progreso de pruebas y publicación durante el proceso de cambios. 

Capacidades principales: 

- Configurar la información de conexión de cada middleware (Punto de acceso, Clave de acceso, USERNAMEPASSWORD, etc.) 
- Cambiar entre diferentes proveedores (S3 y OSS, AWS / MinIO / Tencent Cloud COS / Huawei Cloud OBS, MySQL y DM Dameng) 
- Realizar seguimiento de valores modificados en los formularios; solo se enviarán los campos modificados 
- Cada sección de configuración se puede probar de forma independiente; solo se permite publicar después de pasar la verificación 
- Publicación con un solo clic: enviar por lotes todas las configuraciones de campo modificadas y ejecutar de forma asíncrona 

### 1.1 Usuarios aplicables 

| Rol           | Operaciones comunes                                     | 
| -------------- | ---------------------------------------------------- | 
| Ingeniero de implementación | Rellenar la información de conexión del middleware durante la implementación inicial | 
| Operaciones de guardia | Reemplazo de credenciales, cambios de endpoint, probar conexiones | 
| Respuesta de emergencia | Respaldar middleware de conmutación y modificar configuraciones de tiempo de espera |

### 1.2 Operaciones no recomendadas en este módulo 

Cambio de proveedores (como S3 → OSS) son cambios que implican migración de datos a gran escala y deben manejarse según el proceso de cambios. El ajuste por lotes de la información de conexión en múltiples entornos no está cubierto por este módulo; necesita ingresar a cada entorno individualmente para configurar la página. La planificación de capacidad del middleware y la monitorización de alarmas no están en esta página; por favor, use el módulo de gestión del clúster y configuración de alarmas. 

---

## 2. Explicación detallada de cada configuración del middleware 

### 2.1 S3 Almacenamiento de Objetos 

**Pasos de operación**: Ingrese a 'Configuración de Middleware' desde el menú de la izquierda, que por defecto se encuentra en esta sección → Desplácese hacia abajo para ver tres secciones de configuración en orden: Configuración de instancia pública S3 , Configuración de instancia de Edición Colaborativa S3 y Configuración de Bucket. 

#### 2.1.1 Pública S3 y Edición Colaborativa S3

**Pasos de operación**: Primero complete lo público S3 configuración de la instancia, luego complete la edición colaborativa S3 configuración de la instancia, y finalmente complete la configuración del Bucket. Después de modificar cualquier campo, haga clic en "Probar conexión" en la parte inferior.
Los campos del formulario en ambas secciones son consistentes:
| Campo               | Descripción                                                              | Requerido |
| ----------------- | ---------------------------------------------------------------------- | ---- |
| Tipo de almacenamiento           | Seleccione en el menú desplegable 'S3 (Almacenamiento de Objetos)' o 'OSS (Alibaba Cloud)'                             | Sí   |
| Subtipo               | Cargado dinámicamente según el tipo de almacenamiento: Para S3, las opciones incluyen AWS / Tencent Cloud COS / Huawei Cloud OBS / MinIO / Otros; para OSS, solo Alibaba Cloud OSS está disponible | Sí    |
| ID de Clave de Acceso     | Identificador de Credencial                                                        | Sí   |
| Clave Secreta de Acceso | Clave de credencial, cuadro de entrada PASSWORD enmascarada | Sí |
| Región | Por ejemplo `cn-north-1` | Sí |
| ForcePathStyle | Casilla de verificación, si se habilita el acceso en estilo de ruta | No |
| SSL | Casilla de verificación, si se habilita HTTPS | No |
| Punto de Extremidad | Dirección de acceso al servicio interno | Sí |
| Dirección de Acceso Público | Dirección de acceso del lado del usuario | Sí |
| Regla de Reemplazo de Dirección | Expresión regular o cadena usada para mapear la dirección interna a la dirección pública | Sí |

#### 2.1.2 Configuración del Bucket 

**Pasos de operación**: Todos los Buckets devueltos por el servidor se mostrarán uno por uno, y puede rellenar el CDN nombre de dominio según sea necesario. 

| Campo       | Descripción                  |
| --------- | ------------------------- |
| Nombre del Bucket | Nombre del Bucket         |
| Prefijo      | Prefijo de ruta del almacenamiento de objetos  |
| CDN Dominio  | CDN Dominio de aceleración     |
| Habilitar CDN Autenticación | Casilla de verificación, una vez habilitada, se añadirán dos elementos "Tipo de Autenticación" y "Clave de Autenticación" |

> Después de habilitar CDN la autenticación, se requieren el tipo de autenticación y la clave de autenticación correspondientes. 

### 2.2 Redis
**Pasos**: Use la navegación rápida a la derecha para hacer clic en el Redis icono para desplazarse a esta sección → seleccione un modo → rellene la dirección y PASSWORD → haga clic en "Probar Conexión". 

Descripción del Campo:

| Campo     | Descripción                        |
| -----     | -----------------------------    |
| Modo      | Standalone o Sentinel          |
| Dirección   | Dirección de conexión en modo standalone, p.ej., `redis-sentinel-master-ss:6379` |
| Nombre del Maestro | Requerido en modo Sentinel, por ejemplo, `mymaster` |
| Lista de direcciones | Múltiples direcciones en modo Sentinel, se pueden agregar o eliminar dinámicamente |
| PASSWORD  | Requerido                          |

Cambiar de modo restablecerá automáticamente los campos de Dirección, Nombre del maestro y Lista de direcciones.

### 2.3 Kafka
**Pasos para operar**: Haga clic en el Kafka icono en la navegación rápida a la derecha para desplazarse a esta sección → Complete la dirección del Broker → Si SASL está habilitado, expanda los SASL subcampos → Haga clic en "Probar conexión".

Descripción del Campo:

| Campo         | Descripción                                         |
| ---------- | ------------------------------------------------ |
| Dirección del Broker | Array, se puede agregar o eliminar dinámicamente        |
| Prefijo de tema    | Prefijo agregado automáticamente a todos los temas         |
| Habilitar SASL Autenticación | Interruptor, al habilitarlo agrega tres SASL configuraciones |
| Mecanismo de autenticación | PLAIN / SCRAM-SHA-256 / SCRAM-SHA-512 (después de habilitar SASL) |
| USERNAME / PASSWORD   | SASL credenciales (después de habilitar SASL)           |

### 2.4 MySQL (Base de datos relacional)
**Pasos de operación**: En la navegación rápida a la derecha, haga clic en el RDB icono y desplácese a esta sección → seleccione MySQL o DM Dameng → complete host, puerto, USERNAMEPASSWORD → haga clic en "Probar conexión".

Descripción del Campo:

| Campo      | Descripción        |
| -------- | ---------------- |
| Tipo de base de datos | MySQL o DM (Dameng) |
| Dirección del host  | Por ejemplo `mysql-master` |
| Puerto          | 3306           |
| USERNAME / PASSWORD | Credenciales      |

> La “RDB Base de datos relacional” en el menú derecho y título de la página corresponde a la MySQL sección de configuración.

### 2.5 MongoDB
**Pasos de operación**: Haga clic en el MongoDB icono en la navegación rápida a la derecha para desplazarse a esta sección → Complete la cadena de conexión → Renderice cada credencial de base de datos una por una según la configuración del servidor → Haga clic en "Probar conexión".

Descripción del campo: 

| Campo           | Descripción                       |
| ------------- | -------------------------------- |
| Cadena de conexión | Por ejemplo `mongo-master:27017` |
| Cada base de datos USERNAME / PASSWORD | Renderizada una por una para las bases de datos configuradas en el servidor |

### 2.6 Elasticsearch
**Pasos:** Use la navegación rápida a la derecha para hacer clic en el Elasticsearch icono y desplazarse a esta sección → Complete la dirección del host y el puerto → Si la autenticación está habilitada, rellene USERNAME y PASSWORD → Haga clic en 'Probar Conexión'.

Descripción del campo: 

| Campo     | Descripción       | Requerido |
| ----      | --------------  | ----    |
| Dirección del Host | p.ej., `elasticsearch` | Sí      |
| Puerto        | 9200             | Sí      |
| USERNAME    | Credenciales ES   | No       |
| PASSWORD    | Credenciales ES   | No       |

---

## 3. Operaciones Comunes 

### 3.1 Actualización de Credenciales (p.ej., Rotación de Clave de Acceso) 

1. Vaya a la configuración del middleware 
2. Reemplace el ID de la Clave de Acceso y el Secreto de la Clave de Acceso en la S3 tarjeta pública 
3. Haga clic en "Probar Conexión" y espere el mensaje verde "Prueba de Conexión Exitosa" 
4. Repita la prueba de conexión para otras secciones modificadas 
5. Haga clic en "Publicar Configuración" en la parte inferior 
6. El sistema indica que se ha creado una tarea asincrónica y lo redirige a la pestaña del registro de tareas 

### 3.2 Cambiar Proveedores de Middleware 

1. Vaya a la configuración del middleware 
2. En la tarjeta correspondiente, modifique "Tipo / Subtipo de Almacenamiento" así como el Endpoint del nuevo proveedor, la Clave de Acceso, las reglas de reemplazo de dirección, etc. 
3. Después de la modificación, haga clic en "Probar Conexión" para verificar 
4. Haga clic en "Publicar Configuración" 

> Cambiar de proveedor implica recargar el pool de conexiones para los servicios descendentes, por lo que evite las horas punta; tras el cambio, se recomienda monitorear los registros de la aplicación durante 5 a 10 minutos. 

### 3.3 Habilitar Kafka SASL 

1. Vaya a la configuración del middleware y localice la Kafka sección 
2. Active el interruptor "Habilitar SASL Autenticación", y los SASL campos se expandirán 
3. Complete el mecanismo de autenticación, USERNAME, y PASSWORD 
4. Haga clic en "Probar Conexión" 
5. Después de pasar, haga clic en "Publicar Configuración" 

### 3.4 Recuperarse de una Operación Errónea 

Antes de hacer clic en "Publicar configuración", el estado del formulario se guarda en el localStorage del navegador. Se puede recuperar de la siguiente manera: 

- Haga clic en el botón "Restablecer todo" en la parte inferior, y todos los campos se restaurarán a los valores iniciales del servidor 

### 3.5 Seguimiento de tareas asincrónicas 

Después de publicar la configuración con éxito, el sistema saltará a la pestaña del registro de tareas para mostrar el progreso de las tareas. Las tareas pueden ser largas o cortas, dependiendo del número de instancias de middleware y del número de campos modificados. 

--- 

## 4. Problemas comunes 

**P1: Hacer clic Redis en la navegación rápida en la parte superior derecha no tiene respuesta.**

La navegación rápida del lado derecho solo desplaza hasta la sección correspondiente. Si esa sección no está en la página actual (por ejemplo, bloqueada por un cuadro emergente), puede desplazarse por la página o hacer clic nuevamente en el Redis icono en la navegación lateral para reposicionar.

**P2: Después de publicar la configuración, el estado no parece actualizarse.**

La página se actualizará automáticamente después de publicar. Si el navegador no se actualiza automáticamente, puede presionar F5 manualmente para obtener la configuración más reciente.

**P3: El número en 'N configuraciones modificadas' no coincide con el recuento real.**

La página cuenta basándose en los campos con valores modificados del formulario. En ciertas situaciones, como después de un reinicio y luego una modificación, o al agregar/eliminar dinámicamente elementos de un array, esto puede causar discrepancias en el conteo. Puede hacer clic en 'Restablecer todo' y rellenar las entradas.

**P4: La tarjeta de configuración de Bucket no puede encontrar el Bucket que quiero añadir.**

La página muestra los Buckets existentes según la configuración del lado del servidor. Agregar un nuevo Bucket requiere modificar el archivo de configuración del servidor subyacente, no esta página. Si esto es necesario, por favor contacte al ingeniero de implementación.

---

## Apéndice A: Referencia de Terminología

| Término       | Explicación                                                                |
| -------- | ----------------------------------------------------------------- |
| Archivo de Configuración del Servidor | La fuente de configuración final para el mantenimiento de la plataforma, formada al fusionar los valores predeterminados de la plataforma con los valores emitidos en esta página |
| Bucket   | Buckets de almacenamiento en S3 / OSS |
| Punto de Extremidad | Dirección del servicio de middleware, utilizada para el acceso interno del clúster |
| Dirección de acceso público | Dirección del middleware visible para el usuario |
| Reglas de reemplazo de direcciones | Mapear una dirección interna a una expresión regular o cadena de una dirección pública |
| SASL     | Capa de Autenticación y Seguridad Simple, mecanismos de autenticación para componentes como Kafka |
| Sentinel | Uno de Redislos planes de alta disponibilidad de |
| DM       | Dameng Base de Datos (base de datos relacional nacional)                                             |
| Campo Sucio       | Campos en el formulario que han sido modificados y son diferentes del valor inicial          |
