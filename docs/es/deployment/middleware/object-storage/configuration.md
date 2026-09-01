# Configuración de Almacenamiento de Objetos

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este documento está destinado a guiar al personal de implementación, operaciones o integración para completar la ShimoDocs conexión a almacenamiento de objetos externo de terceros S3 paso a paso.


# 1. Confirmación previa a la operación

## S3 Requisitos de almacenamiento de objetos

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> Preferiblemente nube pública, necesita soportar HTTPS acceso externo



## Conectividad de Red

Los puertos para que K8s la red del clúster de negocio se conecte a la instancia de almacenamiento de objetos deben estar abiertos

```js
telnet IP 9000
```
## Control de acceso y permisos
- Proporcionar información completa de autenticación AK/SK
- Debe soportar completamente interfaces principales como PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload



# S3 Descripción de requisitos de almacenamiento
- Requisitos de latencia: En un entorno de red interna, se recomienda que el tiempo promedio de respuesta del almacenamiento API sea <50 ms; en un entorno de red pública, se recomienda que sea <200 ms. La alta latencia afectará directamente la velocidad de apertura de documentos y la experiencia de carga de archivos adjuntos.
- Capacidad de concurrencia: debe soportar el pico estimado de la empresa QPS. ShimoDocs durante la colaboración de múltiples usuarios y la importación/exportación por lotes, por lo que el almacenamiento no debe tener políticas de limitación de velocidad excesivamente estrictas.
- Disponibilidad SLA: Se recomienda que la disponibilidad del almacenamiento en el entorno de producción sea ≥ 99,9 %.
- La comunicación del almacenamiento en red pública debe realizarse a través de HTTPS/TLS canales cifrados.
- Sincronización de tiempo: El S3 servicio de almacenamiento y ShimoDocs servidor de aplicaciones deben sincronizarse vía NTP, de lo contrario S3 la verificación de la firma fallará.
