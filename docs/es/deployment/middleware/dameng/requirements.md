# Dameng Requisitos V8

[← ShimoDocs Suite Documentación de implementación](../../README.md)

Este documento está destinado a guiar al personal que está implementando, manteniendo o integrando Dameng la Base de Datos por primera vez, para completar Dameng DM8 la inicialización de la base de datos, MySQL la configuración del modo de compatibilidad, el inicio del servicio y la verificación de la conexión paso a paso.

Los ejemplos en este documento utilizan la siguiente planificación:

| Ítem | Valor de ejemplo |
| --- | --- |
| Directorio de instalación de la base de datos | `/opt/dmdbms` |
| Directorio de almacenamiento de la base de datos | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| Nombre de la instancia | `DBSERVER` |
| Puerto de la base de datos | `5236` |
| Cuenta de administrador | `SYSDBA` |
| Administrador PASSWORD | `<SYSDBA_PASSWORD>` |

> Nota: `<SYSDBA_PASSWORD>` y `<SYSAUDITOR_PASSWORD>` en el documento son marcadores de posición. Durante las operaciones reales, por favor reemplácelos con contraseñas reales que cumplan con los PASSWORD requisitos de complejidad.

## 1. Confirmación previa a la operación

### 1. Confirmar que Dameng ya está instalado

Ejecute en el servidor:

```bash
ls /opt/dmdbms/bin
```

Si puede ver archivos como `dminit`, `dmserver`, `disql`, indica que el Dameng software ya ha sido instalado.

También puede verificar la versión:

```bash
/opt/dmdbms/bin/dmserver
```

Contenido como este puede aparecer en la salida:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. Confirmar usuario del sistema

Dameng usualmenteejecuta la base de datos usando el `dmdba` usuario. Compruebe si el usuario existe:

```bash
id dmdba
```

Si no existe, puede ser creado por el `root` usuario:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. Preparar el directorio de datos

Ejecute usando el `root` usuario:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

El propósito de este paso es crear un directorio para almacenar archivos de la base de datos y otorgar permisos al `dmdba` usuario.

## 2. Inicializar la Base de Datos

Cambiar a `dmdba` usuario:

```bash
su - dmdba
```

Ejecute el comando de inicialización: 

```bash
/opt/dmdbms/bin/dminit \
  PATH=/dmdata/data \
  PAGE_SIZE=32 \
  EXTENT_SIZE=32 \
  CASE_SENSITIVE=0 \
  UNICODE_FLAG=1 \
  DB_NAME=DMTEST \
  INSTANCE_NAME=DBSERVER \
  PORT_NUM=5236 \
  SYSDBA_PWD=<SYSDBA_PASSWORD> \
  SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Si la inicialización es exitosa, verá una salida similar a: 

```text
create dm database success
```
Después de una inicialización exitosa, se genera el directorio de la base de datos: 

```text
/dmdata/data/DMTEST
```

El archivo de configuración clave entre ellos es:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. Modificar MySQL Configuración de compatibilidad

Edita el archivo de configuración usando el `root` o `dmdba` usuario:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

Busca y modifica las siguientes dos configuraciones: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

Si el archivo ya tiene estas dos configuraciones, puedes modificar directamente las líneas existentes.

No agregues otra configuración con el mismo nombre al final del archivo, de lo contrario, pueden ocurrir configuraciones duplicadas, causando que el valor efectivo real difiera del esperado.

Después de completar la modificación, puedes verificar usando el siguiente comando:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

Se espera ver:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. Registrar Servicio de Base de Datos

Vuelve a `root` usuario:

```bash
exit
```

Registrar servicio de base de datos: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

Después del registro exitoso, el nombre del servicio usualmente es:

```text
DmServiceDBSERVER.service
```

Configúralo para iniciar al arrancar y arranca el servicio: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

Verifica el estado del servicio: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Si ves: 

```text
Active: active (running)
```

Indica que el servicio de base de datos ha iniciado. 

## 5. Verificar si la Base de Datos está Disponible 

### 1. Verificar el Puerto 

Ejecuta: 

```bash
ss -lntp | grep ':5236'
```

Si ves `dmserver` escuchando en `5236`, indica que el puerto de la base de datos es normal.

### 2. Prueba de Inicio de Sesión Local

Cambiar a `dmdba` usuario:

```bash
su - dmdba
```

Inicia sesión en la base de datos: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

Ejecuta después de un inicio de sesión exitoso:

```sql
select 1 as OK;
```

Si retorna: 

```text
OK
-----------
1
```

Indica que la conexión a la base de datos es normal. 

### 3. Verificar si está en MySQL modo de compatibilidad

Ejecuta en `disql`: 

```sql
select para_name, para_value
from v$dm_ini
where para_name in (
  'COMPATIBLE_MODE',
  'ORDER_BY_NULLS_FLAG',
  'INSTANCE_NAME',
  'PORT_NUM'
);
```

Resultado esperado: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

Entre ellos:

```text
COMPATIBLE_MODE = 4
```

Indica que el estado actual de ejecución de la base de datos ha habilitado MySQL modo de compatibilidad. 



## Apéndice 1, Descripción Detallada de los Ítems de Configuración 

### 1. `PATH` 

Ejemplo: 

```text
PATH=/dmdata/data
```

Significado: 

`PATH` es el directorio raíz de los archivos de la base de datos. Durante la inicialización, Dameng creará el directorio de la base de datos bajo este directorio.

Si `DB_NAME=DMTEST`, el directorio final usualmente es: 

```text
/dmdata/data/DMTEST
```

Este directorio almacenará archivos de datos, archivos de registro, archivos de control y el `dm.ini` archivo de configuración.

Recomendaciones:

- Se recomienda colocarlo en un disco de datos con suficiente capacidad y rendimiento estable en un entorno de producción.
- No se recomienda colocarlo en directorios temporales, como `/tmp`.
- No mueva el directorio de manera casual después de la inicialización.

### 2. `DB_NAME`

Ejemplo:

```text
DB_NAME=DMTEST
```

Significado: 

`DB_NAME` es el nombre del DATABASE_NAME. Esto afectará el nombre del directorio de la base de datos, el nombre del archivo de registro, etc. 

Por ejemplo, cuando `DB_NAME=DMTEST`, normalmente genera: 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

Recomendaciones:

- Use un único DATABASE_NAME claro durante todo el proyecto.
- No se recomienda cambiarlo después de la inicialización.

### 3. `INSTANCE_NAME`

Ejemplo:

```text
INSTANCE_NAME=DBSERVER
```

Significado: 

`INSTANCE_NAME` es el nombre de la instancia de la base de datos. Generalmente se usa para generar el nombre del servicio al registrar un servicio.

Por ejemplo: 

```text
INSTANCE_NAME=DBSERVER
```

El nombre del servicio después del registro generalmente es:

```text
DmServiceDBSERVER.service
```

Recomendación: 

- Para una sola máquina con una sola instancia, se puede usar `DBSERVER`.
- Al desplegar múltiples instancias en una máquina, cada nombre de instancia debe ser diferente.

### 4. `PORT_NUM`

Ejemplo: 

```text
PORT_NUM=5236
```

Significado: 

`PORT_NUM` es el puerto de escucha de la base de datos. Las aplicaciones necesitan acceder a este puerto al conectarse a la base de datos. 

El puerto ingresado en la página del programa debe ser consistente con este: 

```text
HOST:172.17.9.84
PORT:5236
```

Recomendaciones: 

- El puerto predeterminado para Dameng generalmente es `5236`. 
- Si hay múltiples Dameng instancias en la misma máquina, los puertos no pueden duplicarse. 
- Después de cambiar el puerto, el servicio de base de datos necesita reiniciarse. 

### 5. `PAGE_SIZE` 

Ejemplo: 

```text
PAGE_SIZE=32
```

Significado: 

`PAGE_SIZE` es el tamaño de página de la base de datos, en KB. Cuando la base de datos lee y escribe datos, organiza los datos en función de páginas. 

`PAGE_SIZE=32` significa que cada página de datos tiene 32 KB. 

Impacto: 

- Afecta el almacenamiento de datos, la indexación y el comportamiento de E/S. 
- No se recomienda modificar después de la inicialización. 
- Si se necesita ajuste, usualmente requiere reinicializar la base de datos y migrar los datos. 

Recomendaciones: 

- Si hay un SOP para el escenario, configure de acuerdo con el SOP. 
- Cuando no haya requisitos especiales, no lo cambie arbitrariamente. 

### 6. `EXTENT_SIZE` 

Ejemplo: 

```text
EXTENT_SIZE=32
```

Significado: 

`EXTENT_SIZE` es el tamaño del clúster, medido en páginas. Puede entenderse como la unidad básica de asignación de espacio que la base de datos utiliza de una vez.

Si: 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

Entonces un clúster es aproximadamente: 

```text
32KB * 32 = 1024KB
```

Eso es aproximadamente 1MB. 

Impacto: 

- Afectará la granularidad de la asignación de espacio del archivo de datos. 
- No se recomienda modificar después de la inicialización. 

### 7. `CASE_SENSITIVE` 

Ejemplo: 

```text
CASE_SENSITIVE=0
```

Significado: 

`CASE_SENSITIVE` indica si los nombres de los objetos de la base de datos distinguen mayúsculas de minúsculas.

Valores comunes: 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

Por ejemplo, cuando no distingue mayúsculas de minúsculas, los siguientes dos nombres de tabla pueden considerarse el mismo objeto:

```text
user
USER
```

Impacto: 

- Afectará el reconocimiento de nombres de tabla, nombres de campos y nombres de objetos. 
- Para MySQL migración o MySQL-compatibles escenarios, generalmente se prefiere configurarlo como `0`. 
- No se recomienda modificar después de la inicialización. 

### 8. `UNICODE_FLAG` 

Ejemplo: 

```text
UNICODE_FLAG=1
```

Significado: 

`UNICODE_FLAG` es una configuración de conjunto de caracteres.

Valores comunes: 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` indica que la base de datos utiliza el UTFconjunto de caracteres -8.

Recomendación:

- Se recomienda usar UTF-8 para sistemas nuevos.
- Mejor compatibilidad con caracteres chinos, ingleses y multilingües.
- No se recomienda modificar después de la inicialización.

### 9. `SYSDBA_PWD`

Ejemplo:

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

Significado: 

`SYSDBA_PWD` es el PASSWORD para el `SYSDBA` cuenta de administrador.

`SYSDBA` es similar a un superadministrador de base de datos y tiene privilegios de alto nivel.

Recomendación: 

- Use una PASSWORD.
- No use CONTRASEÑAS simples como `SYSDBA`, `123456`, `password`.
- PASSWORD Se recomienda que la longitud sea al menos de 8 caracteres e incluya letras y números.
- No escriba la PASSWORD real en documentos externos.

### 10. `SYSAUDITOR_PWD`

Ejemplo: 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Significado: 

`SYSAUDITOR_PWD` es el PASSWORD de la `SYSAUDITOR` cuenta de administrador de auditoría.

`SYSAUDITOR` se utiliza principalmente para capacidades de gestión relacionadas con auditorías.

Recomendación:

- Use un PASSWORD diferente de `SYSDBA`.
- Use una PASSWORD que cumpla con los requisitos de complejidad.

### 11. `COMPATIBLE_MODE`

Ejemplo:

```text
COMPATIBLE_MODE = 4
```

Significado: 

`COMPATIBLE_MODE` es la configuración de modo de compatibilidad de la Dameng Base de datos, utilizada para controlar con qué tipo de base de datos se alinea en términos de SQL sintaxis, funciones y ciertos comportamientos.

Significados comunes de los valores: 

```text
0:DEFAULT_MODE
1:SQL92
2:Oracle
3:MS SQL Server
4:MySQL
5:DM6
6:Teradata
7:PostgreSQL
8:DB2
```

Este texto está configurado como: 

```text
COMPATIBLE_MODE = 4
```

Indica habilitación MySQL modo de compatibilidad. 

Función: 

- Mejora la compatibilidad de MySQL SQL sintaxis en Dameng. 
- Reduce el costo de transformación al migrar desde MySQL o al adaptarse a Dameng. 

Nota: 

- Esta configuración no significa que Dameng admite el MySQL protocolo. 
- Los programas aún necesitan usar el Dameng controlador internamente; si no hay opción de configuración del controlador en la página, los usuarios no necesitan llenarlo por separado. 
- Se requiere un reinicio del servicio de base de datos después de la modificación. 
- Si finalmente tiene efecto debe basarse en los `v$dm_ini` resultados de la consulta. 

### 12. `ORDER_BY_NULLS_FLAG` 

Ejemplo: 

```text
ORDER_BY_NULLS_FLAG = 0
```

Significado: 

`ORDER_BY_NULLS_FLAG` se utiliza para controlar si NULL los valores aparecen al principio o al final al ordenar con `ORDER BY`. 

Por qué es importante: 

Diferentes bases de datos pueden tener comportamientos predeterminados distintos para ordenar NULLs. Al migrar una aplicación de MySQL a Dameng, si los resultados de la ordenación dependen de la posición de los NULLs, este parámetro puede afectar el orden de los resultados de la consulta. 

Este artículo está configurado como: 

```text
ORDER_BY_NULLS_FLAG = 0
```

El propósito es hacer que el comportamiento de la ordenación sea más cercano a los MySQL hábitos de uso.

Nota:

- Se requiere un reinicio del servicio de base de datos después de la modificación.
- Si la SQL empresa ya especifica explícitamente `NULLS FIRST` o `NULLS LAST`, el comportamiento especificado en la SQL debería tener prioridad.

## Apéndice 2, Preguntas Frecuentes

### 1. ¿Por qué no puedo conectarme usando un MySQL incluso después de configurar el modo de compatibilidad MySQL ?

Porque MySQL el modo de compatibilidad solo afecta SQL sintaxis y algunos comportamientos de la base de datos, no cambia Damengel protocolo de red de 's.

Cuando las aplicaciones o herramientas se conectan a Dameng, el Dameng controlador todavía necesita ser utilizado:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

no puede ser utilizado: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. ¿Cómo confirmar que la configuración realmente tuvo efecto?

No solo mires el `dm.ini` archivo; se recomienda iniciar sesión en la base de datos para verificar el estado en tiempo de ejecución:

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

El estado en ejecución es efectivo solo cuando se observan los siguientes resultados: 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. ¿Por qué no tiene efecto después de modificar `dm.ini`?

Razones comunes:

- El servicio de base de datos no se reinició después de la modificación.
- Hay elementos de configuración duplicados en el archivo.
- El archivo modificado no es el `dm.ini` actualmente utilizado por la instancia.

Puede confirmar qué archivo de configuración está utilizando la instancia actual mediante el comando de inicio del servicio:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Generalmente verás algo como lo siguiente en la salida:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. ¿Qué debo hacer si un PASSWORD ¿Ocurre un error de complejidad durante la inicialización?

Indica que el PASSWORD es demasiado simple. Por favor, cámbialo a un PASSWORD, por ejemplo:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### más complejo. ¿Se pueden cambiar estos parámetros más tarde? 

No. 

En general, no se recomienda cambiar los parámetros de inicialización más tarde, por ejemplo: 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

Si estos parámetros están mal configurados, generalmente se recomienda reinicializar la base de datos y migrar los datos nuevamente. El 

'dm.ini' parámetro se puede ajustar más tarde, por ejemplo: 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

Sin embargo, después de la modificación, generalmente se necesita reiniciar el servicio de la base de datos. 

## Apéndice 3: Lista de Verificación Final 


- El directorio de datos '/dmdata/data' ha sido creado. 
- El host del directorio de datos es 'dmdba:dinstall'. 
- La base de datos se ha inicializado exitosamente. 
- '/dmdata/data/DMTEST/dm.ini' existe. 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- Servicio de la base de datos `DmServiceDBSERVER.service` está `active`. 
- Puerto `5236` está siendo escuchado. 
- `SYSDBA` puede iniciar sesión en la base de datos. 
- En `v$dm_ini`, el valor en tiempo de ejecución de `COMPATIBLE_MODE` está `4`.
