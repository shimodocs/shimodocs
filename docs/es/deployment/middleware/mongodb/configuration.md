# MongoDB Configuración

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este artículo está destinado a guiar al personal de implementación, operación o integración para completar la integración de ShimoDocs con externos MongoDB paso a paso

## 1. Confirmación Antes de la Operación

## MongoDB Requisitos de la Instancia


| Middleware | Versión Recomendada | Menos de 3000 Usuarios | Más de 3000 Usuarios |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |


## Requisitos de Configuración del Clúster
- Soporta clústeres de alta disponibilidad con conjunto de réplicas, al menos 3 nodos son obligatorios en entornos de producción
- Se recomienda habilitar SCRAM-SHA-256 autenticación





## Conectividad de Red

Los puertos para K8s los clústeres de negocio para acceder a MongoDB deben estar abiertos

```js
telnet IP 27017
```

## Autenticación y Autorización
- En entornos de producción, se recomienda hacer cumplir SCRAM-SHA-256 autenticación.


## Otros Requisitos
- Red interna P99 latencia de lectura < 5ms, latencia de escritura < 10ms
- Disco IOPS debe cumplir con los requisitos máximos de escritura; SSD es obligatorio
- Sincronización de reloj: MongoDB los nodos del clúster y ShimoDocs los servidores de aplicación deben estar NTP sincronizados
- Copias de seguridad completas regulares y copias de seguridad continuas de Oplog
