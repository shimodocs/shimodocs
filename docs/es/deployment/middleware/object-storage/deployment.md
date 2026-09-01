# Desplegar con Almacenamiento de Objetos

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo explica cómo deshabilitar el incorporado MinIO en el ShimoDocs y configurar el propio del cliente S3 almacenamiento de objetos como almacenamiento de terceros S3 Una vez completada la configuración, el instalador verificará el S3 conectividad de red del almacenamiento de objetos, información de autenticación y permisos de lectura/escritura de los buckets. Una vez que las comprobaciones se completen, el despliegue puede continuar. 

# 1. Preparación antes de la configuración 
Antes de comenzar, por favor confirme: 
- S3 el almacenamiento de objetos está instalado y funcionando normalmente. 
- K8s los nodos del clúster pueden acceder al host y puerto del S3 almacenamiento de objetos. 
- La autenticación AK/SK para conectar al S3 almacenamiento de objetos está lista. 
- S3 el almacenamiento debe soportar acceso desde el navegador del cliente. 
- Se recomienda usar una S3 instancia separada. 
- Estándar S3 Se deben proporcionar direcciones de acceso por protocolo estándar para redes internas y externas. 
- ShimoDocs estándar de negocio S3 los buckets deben ser creados con antelación. 
- S3 el almacenamiento debe usar SSD discos. 

## ShimoDocs Estándar de Negocio S3 Lista de Buckets 

| Nombre del Bucket | Permiso de Acceso | Orígenes Permitidos | Métodos Permitidos | Modo de Acceso | Exponer_cabeceras | 
| --- | --- | --- | --- | --- | --- | 
| mención automática | Privado Leer/Escribir | - | - | Red Interna |  | 
| componer-cargas | Privado Leer/Escribir | - | - | Red Interna |  | 
| tarea-fc | Privado Leer/Escribir | - | - | Intranet |   |
| conjuntos-de-cambios-de-archivo | Privado Leer/Escribir | - | - | Intranet |
| archivo-calculado | Privado Leer/Escribir | * | GET/HEAD | Intranet Extranet | 
| contenidos-de-archivo | Privado Leer/Escribir | * | GET/HEAD | Intranet Extranet | x-amz-meta-cabeza x-amz-meta-longitud x-amz-meta-bytes x-amz-meta-version-delta x-amz-meta-eek x-amz-meta-cache-de-formulas x-amz-meta-compresor |
| plantillas-de-archivo | Privado Leer/Escribir | - | - | Intranet Extranet |
| historiales-de-hojas | Privado Leer/Escribir | - | GET/HEAD | Intranet Extranet | Access-Control-Allow-Origin x-amz-meta-compresor |
| historial-de-doc-svc | Privado Leer/Escribir | * | GET/HEAD | Intranet | Access-Control-Allow-Origin x-amz-meta-compresor | 
| activos-shimo | Lectura Pública Escritura Privada | * | GET/HEAD | Intranet Extranet | 
| adjuntos-shimo | Privado Leer/Escribir | * | GET/POST/PUT/HEAD | Intranet Extranet |  |  |  |
| imágenes-shimo | Privado leer/escribir | * | GET/POST/PUT/HEAD | Red interna Red externa |  |  |  |
| usuarios-shimo | Privado leer/escribir | - | - | Red interna Red externa |  |  |  |
| avatar-shimo | Lectura pública escritura privada | * | GET | Red interna Red externa  |  |  |  |
| previsualización | Privado leer/escribir | * | GET/HEAD | Red interna Red externa |Aceptar-Rangos x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor  |  |  |
| svc-drive | Privado leer/escribir | * | GET/POST/PUT | Red interna Red externa |Aceptar-Rangos|  |  |
| svc-table | Privado leer/escribir | * | GET/HEAD |  Red interna Red externa |  |  |  |
| instantáneas de archivos |  Lectura/escritura privada|  - | - |  Red interna Red externa |  |  |  |

## Instrucciones de configuración del bucket
- Exponer_Se recomienda que Headers especifique los nombres de los encabezados, y no use el * símbolo, porque algunos proveedores pueden no soportar * símbolo, por ejemplo Huawei Cloud OBS, Alibaba OSS
- El nombre del bucket se puede configurar con un prefijo según las necesidades de la empresa para evitar duplicaciones

# 2. Ingresar en Configuración Avanzada
En el paso 'Configuración' del instalador, después de completar la configuración de red, entorno objetivo e información del nodo, expanda la 'Configuración Avanzada' en la parte inferior de la página.

# 3. Cancele la instalación de la integrada MinIO
En el área 'Servicios de Middleware', desmarque MinIO

Después de desmarcar, el instalador ya no instalará el integrado MinIO, y un externo S3 el almacenamiento de objetos que se ha preparado se usará más adelante. Si otros middleware utilizan servicios integrados debe elegirse de acuerdo con el plan de despliegue real.

# 4. Abrir Configuración de Middleware de Terceros
En el área 'Middleware de Terceros', haga clic en 'Configurar'.

# 5. Configurar S3 Almacenamiento de Objetos
1. Seleccione "S3 Almacenamiento de Objetos" a la izquierda.
2. Habilitar "MinIO Almacenamiento de Objetos."
3. Para la Interacción Servicio Principal/Editor, ingrese respectivamente: AK/SK, punto de enlace interno, punto de enlace público, host, puerto, SSL, y otra información
4. Verifique y guarde

> [!TIP]
>
> Servicio Principal: La instancia de almacenamiento de objetos utilizada para servicios distintos a la edición colaborativa
> Interacción del Editor: La instancia de almacenamiento de objetos utilizada por el servicio de edición colaborativa
>
> Nota: el servicio principal y la interacción del editor pueden usar la misma instancia de almacenamiento de objetos, pero proporcionar una instancia separada para la interacción del editor puede ofrecer un mejor rendimiento en la edición colaborativa

## Nomenclatura de Buckets

> [!NOTE]
>
> Cuando varias aplicaciones de negocio comparten la misma S3 instancia, los clientes pueden agregar prefijos según ShimoDocslas reglas de nomenclatura de buckets para ayudar a distinguir entre diferentes negocios y administrar los buckets

# 6. Confirmar Resultados de Verificación
El instalador verificará lo siguiente:
- inicio de sesión: La cuenta puede autenticarse normalmente
- conectividad: El entorno de implementación puede acceder al S3 almacenamiento de objetos
- permiso: La cuenta tiene los permisos para conexión, autenticación, lectura/escritura de buckets, etc.

Después de que todos los elementos de inspección muestren 'Éxito', cierre la ventana de configuración y vuelva a la página 'Configuración' del instalador.

Si hay algún elemento fallido, verifique según las indicaciones de la página:
- Si el host y el puerto están llenados correctamente.
- Si la red entre el nodo de despliegue y el S3 el almacenamiento de objetos está conectado.
- Si USERNAME y PASSWORD son correctos. 
- Si la cuenta tiene los permisos requeridos (conexión y autenticación, permisos de lectura/escritura de buckets, etc.). 

# 7. Continuar inicializando la implementación 
Después de regresar a la página 'Configuración', asegúrese de que S3 el Almacenamiento de Objetos permanezca sin marcar, luego haga clic en 'Inicializar Implementación' para continuar completando la visión general de la implementación, verificaciones y pasos de ejecución. 

> [!TIP] 
> 
> Antes de inicializar la implementación, confirme nuevamente que la S3 La configuración del Almacenamiento de Objetos ha sido guardada y todos los elementos de validación han pasado.
