# Captura de Paquetes de Contenedores

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## Descripción general de la función 

La captura de paquetes del contenedor se utiliza para recopilar datos de red de Pods en ejecución en un Kubernetes entorno, ayudándole a analizar problemas como fallos de conexión, tiempos de espera de solicitudes, TCP retransmisiones y congestión de la red. 

Después de que se complete la captura, puede descargar el PCAP archivo y analizarlo más a fondo utilizando herramientas de análisis de red como Wireshark. 

## Acceso a la Página 

Después de iniciar sesión en la consola de gestión, seleccione **Captura de Paquetes de Contenedores** en la navegación a la izquierda para entrar en la página. 

La captura de paquetes de contenedor solo es aplicable en Kubernetes entornos de implementación y solo está disponible para administradores. 

## Iniciando una Captura de Paquetes 

Se recomienda seguir estos pasos: 

1. Buscar y localizar el Pod objetivo en la **Lista de Pods**. 
2. Asegúrese de que el estado del Pod sea Ejecutando, luego haga clic en **Iniciar Captura**. 
3. Seleccione la duración de la captura: 1 minuto, 5 minutos o 30 minutos. 
4. Seleccione el tamaño del archivo de captura: 100 MB, 500 MB o 1 GB. 
5. Elija las condiciones del filtro según sea necesario, o ingrese manualmente una expresión de filtro tcpdump. 
6. Revise el comando de captura completo mostrado en la página. 
7. Haga clic **Iniciar Captura** para crear la tarea. 

Sólo se puede ejecutar una tarea de captura de paquetes en el mismo Pod al mismo tiempo. La tarea finalizará automáticamente cuando alcance la duración establecida, o puede ser detenida manualmente. 

## Condiciones de filtro 

Establecer condiciones de filtro puede reducir el tráfico no relacionado y el tamaño del archivo. La página proporciona algunos ajustes preestablecidos comúnmente usados, como: 

- Tráfico en puertos especificados. 
- gRPC tráfico. 
- Host y puerto especificados. 
- HTTP POST solicitudes. 
- TCP establecimiento de conexión, retransmisión o paquetes de ventana pequeña. 

También puede ingresar manualmente usando la sintaxis de tcpdump, por ejemplo: 

```text
host 10.0.0.1 and port 80
```

Si no se especifican condiciones de filtrado, la tarea puede recopilar una gran cantidad de tráfico de red del Pod.

## Administrar tareas de captura de paquetes

En la **Tareas de captura de paquetes** en la página, puede ver el ID de la tarea, Pod, estado, tiempo de creación y tiempo de ejecución.

- **En ejecución**: La tarea puede ser detenida manualmente.
- **Completada**: El PCAP archivo se puede descargar.
- **Fallida**: Puede ver los registros de la tarea para entender la razón del fallo.

La lista de tareas se actualizará automáticamente, o puede hacer clic **Actualizar** para obtener manualmente el estado más reciente.

## Descarga y análisis

Después de que la tarea se complete, haga clic **Descargar** para obtener el PCAP archivo. La función de descarga depende de que el sistema esté correctamente configurado con almacenamiento de objetos.

PCAP Los archivos pueden contener direcciones de solicitud, datos de protocolo u otra información sensible. Por favor, proporciónelos solo al personal autorizado y almacénelos o elimínelos adecuadamente después de su uso.

## Situaciones comunes

- **Pod No Encontrado**: La página solo muestra Pods que están en estado de Ejecución en el entorno actual. Por favor, verifique el estado del Pod y el entorno de implementación.
- **No se Puede Iniciar la Captura de Paquetes**: Por favor, asegúrese de que el Pod no tenga una tarea de captura de paquetes en curso, y verifique Kubernetes los permisos y el soporte para contenedores efímeros.
- **Ejecución de la Tarea Fallida**: Verifique los registros de la tarea para confirmar la expresión del filtro, el estado del Pod y si los componentes de captura de paquetes están funcionando correctamente.
- **No se Puede Descargar el Archivo**: Por favor, verifique la configuración del almacenamiento de objetos y la conectividad de red.
- **Archivo de Captura de Paquetes Demasiado Grande**: Reduzca la duración de la captura y use una expresión de filtro más precisa.

> La captura de paquetes consume ciertos recursos de red, CPUy de almacenamiento. Por favor, evite realizar capturas de larga duración y alto volumen sin filtros durante las horas pico.
