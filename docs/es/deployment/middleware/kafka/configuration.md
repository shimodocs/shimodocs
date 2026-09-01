# Kafka Configuración

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este documento está destinado a guiar al personal de implementación, operación o integración para completar la ShimoDocs integración con middleware de mensajes externo Kafka paso a paso, para escenarios como procesamiento de tareas asíncronas, notificaciones de mensajes, sincronización de datos y entrega de registros de auditoría. 


## 1. Confirmación Pre-Operativa 

## Kafka Requisitos de la Instancia 


| Middleware | Versión Recomendada | Para menos de 3000 usuarios | Para más de 3000 usuarios | 
| --- | --- | --- | --- | 
| Kafka | Kafka 3.5 | 2C 4G 300G SSD | 4C 8G 300G SSD | 


## Requisitos de Configuración 
- Broker >= 3 
- Factor de replicación: el número predeterminado de réplicas es 3; en un entorno de producción es obligatorio tener ≥ 3 para garantizar alta disponibilidad 
- Retención de mensajes: 72 horas (puede ajustarse según las necesidades del negocio) 
- Tamaño máximo de mensaje único por tema: 10 MB 
- Autenticación: soporta SASL acceso cifrado (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512) 





## Conectividad de Red 

Los puertos para acceder Kafka a las instancias desde K8s el clúster empresarial deben estar abiertos 

```js
telnet IP 9092
```



## Otros Requisitos
- Intranet RTT se recomienda que sea < 5 ms; entre centros de datos/regiones se recomienda que sea < 20 ms.
- El ancho de banda debe cumplir con el rendimiento máximo para evitar acumulación de mensajes causada por saturación de la red.
- Asegúrese de que el Kafka El tiempo del corredor y ShimoDocs del servidor de aplicaciones están sincronizados (NTP), ya que la desviación de tiempo puede afectar el orden de los mensajes y TTL el cálculo.
