# Configuración de IA

[← ShimoDocs Suite Documentación de implementación](../../README.md)

La configuración de IA se utiliza para conectar ShimoDocs Suite con los modelos base, modelos de imagen, búsqueda en línea y servicios de Embedding. Después de completar la configuración, las funciones en ShimoDocs Suite como conversaciones de IA, generación de contenido, procesamiento de imágenes y recuperación de conocimiento pueden acceder a los servicios correspondientes. 

## 1. Comprender los Cuatro Tipos de Capacidades Antes de Configurar 

Los propósitos de los cuatro tipos de configuraciones son diferentes, y no necesariamente necesitas configurar todos ellos. Por favor, elige según las ShimoDocs Suite funciones que planeas habilitar. 

| Tipo de Configuración | Propósito | Requiere Configuración | 
| --- | --- | --- |
| Modelo Base | Maneja conversaciones, escritura, resumén, reescritura, preguntas y respuestas, y otras tareas de texto o multimodales | Generalmente requerido al usar funciones de IA |
| Modelo de Imagen | Genera o edita imágenes | Solo requerido al usar funciones de generación o edición de imágenes |
| Búsqueda en Línea | Recupera información de servicios de búsqueda externos para complementar las referencias del modelo | Solo requerido al usar funciones de recuperación en línea |
| Embedding | Convierte texto en vectores para recuperación de base de conocimientos, búsqueda semántica y funciones similares | Solo requerido al usar funciones de recuperación de conocimiento o búsqueda vectorial |

> [!TIP]
>
> La búsqueda en línea suele ser un servicio de búsqueda independiente y no es lo mismo que las capacidades en línea integradas en el modelo base. 

## 2. Acceder a la Configuración de IA 

1. Inicie sesión en la **MDP Plataforma de Operaciones**. 
2. En la parte superior, selecciona **ShimoDocs Suite**.
3. En la barra lateral izquierda, selecciona **Gestión de Inquilinos**.
4. Encuentra la **Configuración de IA** tarjeta.
5. Haz clic en la tarjeta para entrar en la página "Configuración de Modelo de IA y Búsqueda".

## 3. Primero, elige el proveedor del servicio de modelo al que conectarte

Por favor, primero confirme el servicio de modelo que planea usar, luego vaya a la sección correspondiente para la configuración.

| Tipo de Modelo | Proveedor de Servicio |
| --- | --- |
| Modelo Base | Proveedores compatibles con el protocolo de Respuestas de OpenAI |
| Modelo de Imagen | Proveedores compatibles con el protocolo de Imágenes de OpenAI |
| Modelo de Motor de Búsqueda en Internet | Actualmente solo admite Volcengine |
| Modelo de Embedding | Proveedores compatibles con el protocolo de embedding de OpenAI |

## 4. Configuraciones del Modelo de IA

Esta sección se utiliza para configurar GPTservicios relacionados. Por favor, haga que Ingeniería confirme si se deben usar los servicios oficiales de OpenAI, Azure OpenAI, servicios proxy u otras interfaces compatibles, ya que la dirección de solicitud y el ID del modelo pueden variar según el método de conexión.

### 4.1 Modelo Base

El modelo base se utiliza para funciones de diálogo, generación de contenido, resumen, reescritura y comprensión multimodal.

#### Configuración del Proveedor

| Elemento de configuración | Valor de ejemplo | Descripción |
| --- | --- | --- |
| Proveedor | Seleccione OpenAI (o compatible con el protocolo de Respuestas de OpenAI) | Seleccione OpenAI (o compatible con el protocolo de Respuestas de OpenAI) |
| Solicitud URL / Base URL | https://myai.com/v1 | Elija su propia dirección de puerta de enlace de IA compatible con el protocolo OpenAI Responses |
| API Clave | sk-I••••haTO | El API Clave asignada a usted por su puerta de enlace de IA |
| Modelo predeterminado | gpt-5.5 | Modelo compatible con el protocolo OpenAI Responses |

> [!TIP]
>
> El proveedor de modelos configurado aquí debe soportar el modo de transmisión. ShimoDocs La IA, como cliente, siempre enviará `stream: true` al solicitar al proveedor de modelos. Si el proveedor de modelos no soporta el modo de transmisión, la solicitud fallará.

#### Configuración del modelo

| Elemento de configuración | Valor de ejemplo | Notas de desarrollo |
| --- | --- | --- |
| Estado | Habilitado | Necesita estar habilitado |
| ID del modelo | gpt-5.5 | ID de modelo válido |
| Nombre del modelo | gpt-5.5 | Debe coincidir con el ID del modelo |
| Ventana de Contexto | 1024000 | Rellenar según las condiciones reales |
| Entrada de Texto | Habilitado | Necesita estar habilitado |
| Entrada de Imagen | Habilitado | Necesita estar habilitado |

### Modelo de Imagen 4.2

Los modelos de imagen se utilizan para la generación o edición de imágenes. Por favor, rellene los modelos y capacidades realmente soportados por la versión actual.

| Elemento de configuración | Valor de ejemplo | Notas de Ingeniería |
| --- | --- | --- |
| Estado | Habilitado | Necesita estar habilitado |
| Proveedor | OpenAI (o compatible con el protocolo de imagen de OpenAI) | OpenAI (o compatible con el protocolo de imagen de OpenAI) |
| Nombre del modelo | gpt-image-2 | Necesita ser compatible con el protocolo de imagen de OpenAI |
| Solicitud URL / Base URL | https://myai.com/v1 | Seleccione su propia dirección de puerta de enlace de IA compatible con el protocolo de Respuestas de OpenAI |
| API Clave | sk-I••••haTO | El API Clave asignada a usted por su puerta de enlace de IA |
| Características | generación de imágenes, edición de imágenes | Mantener la generación de imágenes por defecto, edición de imágenes |

> [!TIP]
>
> Actualmente solo soporta OpenAI Image API protocolo

### Modelo de Búsqueda en Internet 4.3

La búsqueda en red actualmente solo soporta la configuración de Volcengine.

| Elemento de configuración | Valor de ejemplo | Notas de Ingeniería |
| --- | --- | --- |
| Estado | Habilitado | Habilitar según las necesidades reales. Si se habilita, debe completar los valores de todas las demás entradas en el grupo de configuración actual |
| Proveedor de Servicio | Volcengine | Actualmente solo admite Volcengine |
| API Punto de Extremidad | https://open.feedcoopapi.com/search_api/web_search | Dirección de búsqueda en red predeterminada de Volcengine |
| API Clave | mCmh•••••••• | Obtener del proveedor de servicios de búsqueda en red |
| Configuración de Tiempo de Espera | 120s | Si una sola solicitud de búsqueda en red excede este tiempo, fallará. Se recomienda mantenerlo en 120s |

### Modelo de Embedding 4.4

Los modelos de embedding se utilizan para recuperación en bases de conocimiento y búsqueda semántica. El ID del modelo y la dimensión deben coincidir con la salida vectorial real.

| Elemento de configuración | Valor de ejemplo | Notas de desarrollo |
| --- | --- | --- |
| Proveedor de Servicio | OpenAI (o modelo de embedding compatible con OpenAI) | OpenAI (o modelo de embedding compatible con OpenAI) |
| Base URL | https://myai.com/v1 | Elija su propia dirección de puerta de enlace de IA compatible con el protocolo de Respuestas de OpenAI |
| API Clave | ak-•••••••• | Obtener del proveedor del modelo de embedding |
| Modelo de Embedding | qwen3-embedding:4b | ID del modelo |
| Dimensión | Valor entero | La dimensión está relacionada con el modelo de incrustación; puede consultar al proveedor sobre los parámetros de dimensión |

| Elementos de Confirmación de Desarrollo | Contenido |
| --- | --- |
| Modelos de Incrustación Compatibles | OpenAI (o modelo de embedding compatible con OpenAI) |
| Dimensión Vectorial Recomendada | Relacionado con el modelo de incrustación |
| ¿Es necesario reconstruir los datos vectoriales para diferentes dimensiones? | Sí |

> [!TIP]
>
> Actualmente solo soporta OpenAI Embedding API protocolo

### 4.5 GPT Verificación de Configuración Completada

| Elemento de Verificación | Resultado Esperado | Resultado Actual |
| --- | --- | --- |
| Conversación con Modelo Básico | Ingrese una pregunta simple en la sesión de IA | El modelo devuelve el resultado correspondiente |
| Procesamiento de Texto Largo | Salida de texto largo | El modelo devuelve el resultado correspondiente basado en el contenido de texto largo |
| Entrada de Imagen o Procesamiento de Imagen | Ingrese una imagen para reconocimiento | Puede devolver el contenido reconocido |
| Búsqueda en Internet | Indíquele que recupere información de vuelos o boletos de tren | Puede devolver resultados de vuelos o boletos de tren |
| Vectorización de Incrustación | Use palabras clave para búsqueda en IA a través del sitio | Puede devolver el contenido coincidente esperado |

## 5. Significado Comercial de Cada Elemento de Configuración

Esta sección proporciona una explicación unificada del propósito de cada elemento de configuración en la página. Al configurar por primera vez, puede completar según la plantilla del proveedor mencionada anteriormente y luego volver a esta sección para confirmar si cada campo cumple con los requisitos comerciales reales.

### 5.1 Elementos de Configuración del Proveedor de Modelo Básico

| Elemento de configuración | Significado Comercial | Impacto Común de una Entrada Incorrecta | Requerido |
| --- | --- | --- | --- |
| Proveedor | Indica al sistema qué método de adaptación del modelo usar. Incluso si dos servicios son compatibles con interfaces similares, la opción del proveedor puede determinar el formato de la solicitud, el método de autenticación y la forma en que se analizan los resultados. | Puede fallar al guardar, desajuste del formato de la solicitud o la respuesta no puede ser analizada. | Sí |
| Solicitud URL / Base URL | La dirección de entrada ShimoDocs Suite a la que se accede al enviar solicitudes al servicio de modelo. | No se puede conectar al modelo si la dirección es incorrecta; la interfaz puede parecer inexistente si el nivel de la ruta es incorrecto. | Sí |
| API Clave | Credencial utilizada por el servicio de modelo para identificar al solicitante y verificar permisos. | Normalmente indica fallo de autenticación si es incorrecta, ha expirado o los permisos son insuficientes. | Sí |
| Modelo predeterminado | El modelo que el sistema prioriza al llamar cuando las funciones de negocio no especifican explícitamente un modelo. | Algunas funciones de IA pueden no estar disponibles si no se configura o se configura un modelo no disponible. | Sí |

### 5.2 Elementos básicos de configuración del modelo

| Elemento de configuración | Significado Comercial | Impacto Común de una Entrada Incorrecta | Requerido |
| --- | --- | --- | --- |
| Estado | Controla si se permite llamar al modelo mediante ShimoDocs Suite. Después de cerrar, la configuración puede mantenerse, pero normalmente el negocio no puede continuar utilizando el modelo. | Incluso si la configuración del modelo es correcta, si el estado está cerrado, el negocio puede seguir mostrando que no está disponible. | Sí |
| ID del modelo | El nombre del modelo o identificador único reconocido por la interfaz del servicio de modelo. | Normalmente indica que el modelo no existe si no coincide con el nombre del servidor. | Sí |
| Nombre del modelo | El nombre mostrado a los administradores en la Plataforma de Operaciones para ayudar a distinguir diferentes modelos. | Si el nombre está duplicado o no es claro, es fácil seleccionar el modelo incorrecto; si participa en solicitudes reales lo confirma el Departamento de Ingeniería. | Sí |
| Ventana de Contexto | La cantidad total de información que un modelo puede procesar en una sola solicitud, afectando usualmente la longitud del texto de entrada, el historial de conversaciones y el espacio de salida. | Configurar un valor mayor que la capacidad real del modelo puede causar fallas en la solicitud; configurarlo demasiado bajo puede resultar en que el contenido se trunque o no pueda ser enviado. | Sí |
| Entrada de Texto | Indica si el modelo puede aceptar contenido de texto. | Si se configura incorrectamente como apagado, las funciones relacionadas con el texto pueden no poder seleccionar o llamar a este modelo. | Sí |
| Entrada de Imagen | Indica si el modelo base puede entender imágenes cargadas por el usuario; esta es una capacidad de entrada multimodal y no es lo mismo que generar imágenes. | Habilitarlo para un modelo que no soporta imágenes puede causar fallas en la solicitud; si se desactiva, la función de comprensión de imágenes no estará disponible. | Sí |

### 5.3 Opciones de Configuración del Modelo de Imágenes

| Elemento de configuración | Significado Comercial | Efectos Comunes de Configuraciones Incorrectas | Requerido |
| --- | --- | --- | --- |
| Estado | Controla si el modelo de imágenes puede ser llamado por funciones de generación o edición de imágenes. | Si el estado está apagado, las funciones relacionadas con imágenes no pueden usar el modelo. | Sí |
| Proveedor de Servicio | Determina el método de adaptación de la interfaz usado para solicitudes de imágenes. | Elegir incorrectamente puede resultar en parámetros de solicitud o formatos de retorno incompatibles. | Sí |
| Nombre del Modelo / ID del Modelo | Especifica el modelo de imágenes real que se llamará. Si este campo es el nombre de visualización o el ID de solicitud debe ser aclarado por Ingeniería. | Si el nombre no coincide con el servidor, puede indicar que el modelo no existe. | Sí |
| Base URL | La dirección del servicio a la que se envían las solicitudes de generación o edición de imágenes. | Si la dirección o ruta es incorrecta, el servicio de imágenes no puede ser llamado. | Sí |
| API Clave | La credencial de autenticación utilizada para llamar al servicio de imágenes. | Errores, expiración o falta de permisos causarán fallos de autenticación. | Sí |
| Características | Declara las capacidades de imagen soportadas por el modelo, como generación de imágenes, edición de imágenes, etc. | Si se configura una capacidad no soportada por el modelo, la entrada de negocio puede ser visible pero la llamada fallará. | Sí |

Nota: Actualmente, solo se soporta la imagen de OpenAI API protocolo

### 5.4 Configuración de Búsqueda en Internet

| Elemento de configuración | Significado Comercial | Impacto Común Si es Incorrecto | Requerido |
| --- | --- | --- | --- |
| Estado | Controla si ShimoDocs Suite puede llamar al servicio de búsqueda actual. | Cuando el estado está apagado, el modelo aún puede estar disponible, pero no puede obtener resultados de búsqueda en internet. | No |
| Proveedor de Servicio | Especifica el tipo de servicio de búsqueda a usar y su método de adaptación de interfaz. | Si se elige incorrectamente, las solicitudes y el análisis de resultados pueden ser incompatibles. | No |
| Dirección de la Interfaz | El punto de servicio accedido al iniciar una solicitud de búsqueda. | Si la dirección es incorrecta, la función de internet puede tener tiempo de espera o fallar al conectarse. | No |
| API Clave | Credencial de autenticación usada por el servicio de búsqueda. | Si es incorrecta o insuficientes permisos, las solicitudes de búsqueda serán denegadas. | No |
| Configuración de Tiempo de Espera | Tiempo máximo para esperar una sola búsqueda; si se excede, el sistema deja de esperar y lo trata como un fallo o sin resultado. | Configurar un tiempo demasiado corto causará tiempos de espera frecuentes; configurarlo demasiado largo aumentará el tiempo de espera del usuario. | No |

### 5.5 Configuración de Embedding

Los modelos de embedding no necesariamente necesitan estar habilitados, pero si no están habilitados, el contenido del documento no puede ser vectorizado y, por lo tanto, el sistema no puede manejar preguntas relacionadas con la base de conocimiento del usuario.

| Elementos de Configuración | Significado Comercial | Consecuencias Comunes de Completar Incorrectamente | ¿Es Obligatorio? |
| --- | --- | --- | --- |
| Base URL | La dirección del servicio enviada a la solicitud de vectorización de texto. Si la dirección es incorrecta, no se pueden generar ni actualizar datos vectoriales. No |
| API Clave | Credenciales de autenticación usadas por el servicio de Embedding. La vectorización falla debido a un error, caducidad o falta de permiso. No |
| Modelo de Embedding | El ID del modelo realmente responsable de convertir texto en vectores. No se pueden generar vectores cuando el modelo no existe o no coincide. No |
| Dimensión | La longitud final del vector generado por cada línea de texto debe coincidir con la salida real del modelo y la configuración de almacenamiento de vectores. | Si las dimensiones son inconsistentes, escribir o recuperar usualmente no es posible; después de cambiar las dimensiones, es posible que necesite regenerar vectores existentes. No |

Nota: Actualmente, solo el protocolo de Embedding de OpenAI API protocolo 

## 6. Secuencia de configuración recomendada 

Para reducir la duplicación, se recomienda configurar según el siguiente orden: 

1. Primero, confirme qué funciones de IA necesitan ser habilitadas en ShimoDocs Suite. 
2. Seleccione un modelo base que cumpla con los requisitos del protocolo. 
3. Configure el Proveedor y agregue al menos un modelo base. 
4. Establezca el modelo disponible validado como modelo predeterminado. 
5. Configure los modelos de imagen según las necesidades del negocio. 
6. Configure la búsqueda en red según las necesidades del negocio. 
7. Si se utilizan bases de conocimiento o búsqueda semántica, configure entonces Embedding. 
8. Después de guardar, verifique cada capacidad por separado; no juzgue el éxito de la configuración únicamente por la visualización de "Habilitar" en la página. 

## 7. Reglas de Configuración Efectivas 
| Problemas que requieren confirmación de Ingeniería | Contenido |
| --- | --- |
| ¿Tiene efecto inmediatamente después de guardar la configuración? | No tiene efecto de inmediato; necesita esperar 1-2 minutos |
| ¿Necesita reiniciar el servicio? | No es necesario reiniciar el servicio |
| ¿La nueva configuración tiene efecto en una página abierta? | Necesita actualizar la página actual |
| Selección de prioridad entre múltiples modelos | No soportado |
| ¿Se cambia automáticamente cuando el modelo predeterminado no está disponible? | No soportado |

## 8. Solución de Problemas Comunes 

| Fenómeno | Causas Comunes | Método de Solución de Problemas |
| --- | --- | --- |
| Falla en el Servicio del Modelo de Conexión | Anomalías en la Dirección de Solicitud, Red, Certificado o Configuración de Puerto | Verificar Dirección del Servicio, DNSPuerto, Certificado y Políticas de Firewall | 
| Error de autenticación | API Error de clave, expirada o permisos insuficientes | Confirmar que la API Clave es correcta y tiene acceso al modelo o servicio objetivo | 
| Modelo no existe | ID del modelo no coincide con el nombre del servidor | Confirmar el ID completo del modelo y verificar mayúsculas, minúsculas y sufijo de versión | 
| Texto disponible pero imágenes no disponibles | El modelo no soporta entrada de imagen, o el interruptor de entrada de imagen no está habilitado | Verificar capacidad del modelo y el interruptor de entrada | 
| Entrada de funciones de imagen existe pero falla al llamar | Las funciones son inconsistentes con las capacidades reales del modelo | Verificar las capacidades de generación y edición soportadas por el modelo de imagen | 
| Tiempo de espera frecuente en búsqueda en línea | El servicio de búsqueda es lento, la red es inestable, o el tiempo de espera está configurado demasiado corto | Verificar latencia de la red, rendimiento del servicio y configuraciones de tiempo de espera | 
| Falló la escritura de embeddings | Las dimensiones de salida no son consistentes con la configuración de almacenamiento vectorial | Verificar las dimensiones de salida reales del modelo y la configuración de almacenamiento | 

## Preguntas y Respuestas

1. ¿Cómo verificar si la configuración es efectiva?

Después de completar la configuración, puedes ir a la barra lateral del editor para abrir una sesión de IA y verificar si la función está funcionando:

- Los mensajes deben responderse normalmente
- Si se configuró un modelo de imagen, puedes enviar un comando como 'Generar una imagen de Xxx' y observar si el comando se ejecuta correctamente
- Si se configuró la búsqueda en línea, puedes enviar un comando como 'Buscar en línea el clima de hoy en Beijing' y observar si el resultado cumple con la expectativa

2. ¿Admite la interfaz /chat/completions?

No está soportado por ahora. Actualmente solo se soporta el protocolo OpenAI Responses API Se sabe que APIs oficiales como Deepseek / Xiaomi-Mimo proporcionan soporte para este protocolo. Soluciones de implementación local como vLLM y Ollama también soportan el protocolo Responses.
