# MySQL 8 Requisitos

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este documento está destinado a guiar al personal de implementación, operaciones o integración para completar la ShimoDocs conexión a una MySQL inicialización de base de datos 8, así como el inicio del servicio y la verificación de la conexión paso a paso.


## 1. Confirmación previa a la operación

## MySQL Confirmación de Especificaciones de la Instancia

| Versión Recomendada | Usuarios por debajo de 3000 | Usuarios por encima de 3000 | 
| --- | --- | --- |
| MySQL 8.0 | 4C 8G 200G SSD | 8C 16G 200G SSD | 

## MySQL Requisitos de Configuración y Alta Disponibilidad
Soporta conmutación de alta disponibilidad maestro-esclavo
Conjunto de caracteres: utf8mb4 
Zona horaria: Asia/Shanghai o UTC 
Conexiones: máximo_conexiones ≥ 1000
Usuario de conexión: privilegios de administrador

> [!TIP]
>
> Debe configurar una MySQL instancia separada;
> 1. Para lograr aislamiento de fallos, seguridad de permisos y respaldo y recuperación independientes, asegurando el funcionamiento estable y eficiente del sistema de documentos.
> 2. Actualmente, el sistema no admite nombres de bases de datos personalizados ni prefijos de tablas, por lo que la planificación y preparación de una instancia separada debe completarse antes del despliegue.





## Conectividad de Red 
Los puertos para conectar la red del clúster de negocio k8s a la MySQL instancia necesitan estar abiertos 

```js
telnet IP 3306
```
## Autenticación de Usuario
El MySQL el usuario proporcionado necesita ser autenticado al conectar al MySQL servidor

# Explicación: 
- El MySQL Las configuraciones en el documento son todos ajustes recomendados, utilizados para la evaluación de capacidad del proyecto en etapa temprana y planificación de recursos, y no son configuraciones finales de producción. La configuración final real se determinará después de la evaluación pre-venta
- Cuando se utilicen servidores con arquitectura nacional CPU se recomienda estimar los recursos totales al doble de la especificación estándar
- Se recomienda reservar capacidad para expansión en el entorno de producción formal y priorizar el despliegue de alta disponibilidad
