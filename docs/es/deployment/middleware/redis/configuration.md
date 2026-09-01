# Redis Configuración

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este documento está destinado a guiar a los implementadores, personal de operaciones o integradores para completar la integración de ShimoDocs con externos Redis paso a paso. Se utiliza típicamente para escenarios centrales como la gestión de sesiones, bloqueos distribuidos, contadores de limitación de tasa y colas de mensajes

## 1. Confirmación previa a la operación

## Redis Requisitos de la Instancia


| Middleware | Versión Recomendada | Para menos de 3000 usuarios | Para más de 3000 usuarios |
| --- | --- | --- | --- |
| Redis | Redis 6.2.21 | 2C 4G 100G SSD | 2C 8G 100G SSD |


## Requisitos de Configuración del Clúster
- Soporta alta disponibilidad maestro-esclavo/sentinel
- Persistencia de datos
- No se soporta el modo cluster
- El número de DBs debe ser >= 64





## Conectividad de Red

Los puertos para conectar la K8s red del clúster de negocios a la Redis instancia deben estar abiertos

```js
telnet IP 6379
```

## Autenticación y Autorización
- En entornos de producción, se recomienda habilitar PASSWORD autenticación (requirepass / ACL).


## Otros Requisitos
- Red interna P99 la latencia debe ser < 10ms
- Sincronización de reloj: Redis los nodos del clúster y ShimoDocs los servidores de aplicación deben estar NTP sincronizados
- Copias de seguridad completas regulares
