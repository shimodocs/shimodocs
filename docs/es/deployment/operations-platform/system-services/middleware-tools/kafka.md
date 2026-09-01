# Kafka Herramientas

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

> [!TIP]
>
> El Kafka La herramienta le permite ver Kafka estado del clúster, temas, mensajes, grupos de consumidores e información de particiones a través de la Consola Redpanda, comúnmente usada para solucionar problemas de escritura de mensajes, acumulación de consumidores y problemas de enlaces asíncronos.
>
> Una vez que la página se cargue correctamente, la Consola Redpanda se incrustará dentro de MDP.

## 1. Accediendo a Kafka

1. Inicie sesión en la **MDP Plataforma de Operaciones**.
2. Seleccionar **Servicios del sistema** en la parte superior.
3. Expandir **Herramientas de Middleware** en la barra de navegación izquierda.
4. Seleccionar **Kafka**.
5. Espere a que la Consola Redpanda termine de cargarse.

Si la Consola no está lista, la página indicará que se está iniciando o que el inicio falló y mostrará información del error.

## 2. Ver Resumen del Clúster

Después de ingresar Kafka, **Resumen** se muestra de manera predeterminada.

Puede ver la siguiente información:

| Información | Descripción |
| --- | --- |
| Estado del Clúster | Estado de funcionamiento del clúster. |
| Tamaño de Almacenamiento del Clúster | Tamaño actual de almacenamiento del clúster. |
| Versión del Clúster | Información de la versión del clúster. |
| Brokers en Línea | Número de brokers en línea. |
| Temas | Número de temas. |
| Réplicas | Número de réplicas. |
| Detalles del Broker | ID del broker, estado y tamaño. |

## 3. Ver Temas

1. En la navegación izquierda de la Consola Redpanda, seleccione **Temas**.
2. Encuentre el tema objetivo en la lista de temas.
3. Haga clic en el tema para ingresar a la página de detalles.
4. Vea información como las particiones del tema, mensajes y configuración.

La solución de problemas del tema suele centrarse en:

| Información | Descripción |
| --- | --- |
| Particiones | El estado de las particiones del Tema. |
| Mensajes | La lista de mensajes en el Tema. |
| Configuración | Configuración del Tema, como la política de retención. |

## 4. Visualización de Mensajes

1. Ingrese al Tema objetivo.
2. Abra el área de visualización de mensajes.
3. Seleccione partición, posición o intervalo de tiempo utilizando los filtros proporcionados en la página.
4. Vea Clave, Valor, Encabezados, Partición, Desplazamiento y Marca de tiempo del mensaje.

> El contenido del mensaje puede contener campos de negocio. Al solucionar problemas, priorice localizar por ID de negocio, Clave, Desplazamiento y marca de tiempo.

## 5. Visualización de Grupos de Consumidores

1. En la Consola Redpanda, seleccione **Grupos de Consumidores** desde la navegación izquierda.
2. Busque o seleccione el grupo de consumidores objetivo.
3. Ingrese los detalles del grupo de consumidores.
4. Vea los Topics asociados al grupo de consumidores, particiones, Offset actual, Log End Offset y Lag.

## 6. Determinación del Retraso del Consumidor

| Estado | Descripción |
| --- | --- |
| El Lag es 0 | El grupo de consumidores actual no tiene retraso. |
| El Lag está aumentando continuamente | La velocidad de consumo es menor que la velocidad de producción. |
| El Lag no cambia pero no es 0 | Puede haber un consumidor detenido, bloqueo de partición o fallo de consumo. |
| El Lag de una sola partición es significativamente alto | Puede deberse a una clave caliente o consumo anormal en esa partición. |

## 7. Visualización de Brokers

1. En la página de Resumen, busque **Detalles del Broker**.
2. Verifique el ID del Broker, estado de funcionamiento y tamaño de almacenamiento.
3. Haga clic **Ver** para ver los detalles del broker.

## 8. Escenarios Comunes de Solución de Problemas

| Escenario | Sugerencias Operativas |
| --- | --- |
| Confirme si Kafka está funcionando normalmente | Verifique el Estado del Cluster y Brokers en Línea en el Resumen. |
| Confirme si se están escribiendo mensajes | Vaya al Topic y verifique Mensajes. |
| Solucione retrasos de consumidores | Vaya a Grupos de Consumidores y verifique Lag. |
| Ubique un solo mensaje | Busque por Topic, Partición, Offset, Clave o marca de tiempo. |
| Confirme la configuración del Topic | Vaya a los detalles del Topic y verifique Configuración. |
