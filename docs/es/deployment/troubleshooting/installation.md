# Solución de Problemas de Instalación

[← ShimoDocs Suite Documentación de implementación](../README.md)

> [!TIP]
>
> Los problemas comunes durante la fase de instalación generalmente se agrupan en las siguientes categorías.

## 1 Desincronización de tiempo

Síntomas del problema:

* Fallo de inicio de sesión

* Errores de autenticación

* Excepciones en llamadas a servicios

Requisitos de manejo:

* Primero verifique la desviación de tiempo de todos los nodos

* Después de reparar el NTP/servicio de sincronización de tiempo, continúe con la instalación o aceptación

Comandos de investigación:

```plain
timedatectl status
date
```


## 2 Error de configuración de la ruta del disco de datos

Fenómeno:

* Después de la instalación, el disco se llena rápidamente

* Fallo en la escritura de datos

* El directorio persistente se encuentra en el disco del sistema

Requisitos de procesamiento:

* El directorio persistente debe apuntar explícitamente al disco de datos

* Los datos comerciales no deben almacenarse en el directorio del disco del sistema

Comando de resolución de problemas:

```plain
findmnt -n -o TARGET -T /data
df -Th|egrep -v "overlay|tmpfs"
```


## 3 Conexión al servicio de dependencia fallida

Fenómeno:

* La inspección del servicio falla durante la instalación

* Falla la conexión a la base de datos, caché, cola de mensajes o almacenamiento de objetos

Requisitos de manejo:

* Primero verifique si la dirección, el puerto, la cuenta y PASSWORD se ingresan correctamente

* Luego verifique la conectividad de la red y las políticas de seguridad

* Finalmente, verifique si el servicio objetivo en sí está disponible

Comandos de resolución de problemas:

```plain
nc -zv <MYSQL_HOST> 3306
nc -zv <REDIS_HOST> 6379
nc -zv <MONGO_HOST> 27017
nc -zv <KAFKA_HOST> 9092
```


## 4 Incompatibilidad del paquete fuera de línea

Fenómeno:

* Carga del espejo fallida

* El proceso de instalación informa que el servicio no puede iniciarse y la versión no coincide

* El paquete de instalación no corresponde al paquete espejo fuera de línea

Requisitos de procesamiento:

* Confirme que el paquete de instalación, el paquete de imagen sin conexión y la versión del producto sean consistentes

* Confirme si el paquete de instalación coincide con la CPU arquitectura

* Confirme que no se mezclen materiales de diferentes proyectos o diferentes fechas

## 5 La página del instalador no se puede abrir

Fenómeno:

* No se puede acceder a la página de la interfaz web

* El puerto 18080 no está escuchando

* El proceso del instalador ha salido

Comando de resolución de problemas:

```plain
ps -ef | grep mdp | grep -v grep
ss -lntp | grep 18080
tail -n 100 /root/nohup.out
```


## 6. Orden recomendado para la solución de problemas

Solucione problemas de instalación en el siguiente orden:

1. Primero, confirme si es un problema de entorno: sistema, hora, disco, puerto, red

2. Confirme nuevamente si es un problema de configuración: nombre de dominio, directorio, dirección de dependencia, cuenta PASSWORD

3. Confirme nuevamente si es un problema de material: paquete de instalación, paquete sin conexión, compatibilidad de arquitectura

4. Finalmente, verifique el registro del instalador y el estado de ejecución del servicio

Explicación:

* No repita la instalación si no se cumplen los prerrequisitos

* No ejecute comandos con la misma razón de fallo explícita repetidamente

## 7. Cuándo debe detenerse la instalación

Si ocurren las siguientes situaciones, detenga la instalación primero y continúe solo después de solucionar los problemas subyacentes:

* Todos los tiempos de los nodos están desincronizados

* El disco de datos no está montado de forma independiente

* El servicio dependiente externo no es accesible

* La versión del material sin conexión no es consistente

* El servicio del instalador no se inició correctamente
