# Registros del Servicio

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción general de la función 
La función de registros de servicios es una plataforma de recuperación de registros similar a Kibana que puede recopilar registros de Pods de varios ShimoDocs servicios y proporciona capacidades para búsqueda de registros, consulta y análisis de proporciones. 

## Entrada y Navegación
Menú lateral: Servicios del Sistema --> Operaciones de Servicio --> Registros de Servicio

## SQL Modo
El cuadro de entrada admite consultas con sintaxis ClickHouse SQL . Después de ingresar SQL, puede ejecutar la consulta en modo ClickHouse Raw.

Como se muestra en la figura a continuación, ingresar

``` sql
`_raw_log_` like '%access%'
```

puede usarse para consultar todos los registros que contengan access. 

## Filtrado Condicional
Como se muestra en la figura a continuación, haga clic en el botón "Agregar condición" para añadir una nueva condición de filtro.

## Análisis de Proporciones
Como se muestra en la figura a continuación, haga clic en el ícono junto a un campo en el registro de la fila para abrir el menú desplegable. Después de seleccionar "Valores Principales", puede ver la proporción de ese campo dentro del rango de tiempo actual en la esquina superior derecha.

## Descripción de Campos

| Campo Incorporado | Descripción |
| --- | --- |
| lv | El nivel de error del registro, incluyendo info, error, warn |
| container.name | CONTAINER_NAME |
| method | El método en el registro de acceso; gRPC imprime el gRPC método, HTTP imprime el API path |
| peerIP | La IP del par |
| nombreDelPar | El nombre del par, como el nombre del servicio, etc. |
| comp | El componente en el registro de acceso, como server.begin |
| costo | El tiempo consumido en el registro de acceso, en milisegundos |

## Análisis de Casos
### Consultar Todos los Registros de Errores del Día

En Agregar Condición, selecciona el campo lv y agrega lv = error como se muestra en la figura a continuación

### Ver Registros de Solicitudes

    1. Use `msg`='access' para ver todos los registros de solicitudes, incluyendo HTTP y gRPC
    
2. Ver HTTP solicitudes

3. Ver gRPC solicitudes

