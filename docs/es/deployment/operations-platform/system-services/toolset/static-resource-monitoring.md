# Monitoreo de Recursos Estáticos

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

La monitorización de recursos estáticos se usa para verificar el JS y CSS los recursos referenciados por las páginas web, ayudándole a entender el estado de acceso a los recursos, protocolos de transmisión, configuración de caché y CDN uso.

> El nombre de la función en la página del sistema es 'Detección de Recursos Estáticos'.

## 1. Cómo usar

Inicie sesión en la plataforma de gestión, seleccione **Servicios del sistema** en la parte superior, y luego elija **Colección de Herramientas > Detección de Recursos Estáticos** en la barra de navegación izquierda.

Esta función solo está disponible para administradores. Si no ve la entrada, por favor verifique los permisos de su cuenta y la versión actual del producto.

1. Ingrese la página completa URL, por ejemplo `https://example.com/recent`.
2. Si la página requiere inicio de sesión, expanda 'Encabezados de Solicitud Personalizados' y complete la información necesaria como `Cookie` y `Authorization`.
3. Haga clic en 'Iniciar Detección' y espere a que se devuelvan los resultados.

> Los encabezados de solicitud también se utilizarán para acceder a los recursos estáticos referenciados por la página. Por favor, use únicamente credenciales temporales y asegúrese de que los dominios de los recursos de origen cruzado sean confiables. La dirección de la página, los encabezados de solicitud y los resultados más recientes de la detección se guardarán en el navegador actual.

## 2. Alcance de la Detección

El sistema identificará JS y CSS referenciado directamente en la página HTML, pero no detecta imágenes, fuentes, código en línea ni recursos cargados dinámicamente después de la ejecución del script.

- Se detectarán hasta 3 archivos JS y CSS del mismo dominio para cada dominio;
- Se pueden detectar hasta 50 recursos de origen cruzado a la vez;
- Las URL duplicadas se cuentan solo una vez.

Los recursos del mismo dominio que no se detecten se marcarán como “muestreo del mismo dominio omitido,” y esto no indica un error de recurso.

## 3. Visualización de Resultados

Después de completar la detección, la página mostrará:

- **Información Resumida**: Número de HTML recursos, número detectado, número de problemas, uso de caché, CDN, y HTTP/2;
- **Respuesta de la Página**: Código de estado, protocolo e información de caché de la página objetivo;
- **Lista de Recursos**: URL, código de estado, protocolo, caché, CDN, problemas y encabezados de respuesta de cada recurso.

La lista de recursos admite filtrado por 'Detectados', 'Todos' y 'Problemáticos'. 

El sistema indica principalmente los siguientes problemas: 

- HTTP 4xx/5xx; 
- No se detectó caché válido; 
- HTTP/2 no utilizado; 
- Los recursos de origen cruzado no muestran CDN características; 
- Tiempo de solicitud agotado o falla en la resolución del nombre de dominio. 

## 4. Problemas comunes 

### Detección de Página Fallida 

Por favor, revise la página URL, la conectividad de red, HTTPS el certificado y el estado de inicio de sesión. El servicio de detección no reutiliza automáticamente la información de inicio de sesión desde el navegador, por lo que, si es necesario, proporcione encabezados de solicitud personalizados. 

### Recurso No Reconocido 

Por favor, asegúrese de que la página devuelva normal HTML. Los recursos cargados dinámicamente por scripts no serán reconocidos. 

### CDN Muestra 'No Detectado' 

Este resultado solo indica que no se detectaron CDN características en la respuesta y no significa que el recurso definitivamente no esté usando un CDN. Por favor, verifique con la CDN consola y la arquitectura de red. 

## 5. Notas 

- Los resultados de detección reflejan lo que se observó desde la red de la plataforma de gestión durante esta solicitud y pueden diferir de la experiencia real del usuario. 
- CDN, la caché y el estado del problema son resultados determinados automáticamente y son solo para diagnóstico auxiliar. 
- 'No se encontraron problemas' no significa que la página haya pasado una evaluación completa de rendimiento, seguridad o usabilidad. 
- Después de que la página se publique, la CDN se actualiza o el entorno de red cambia, se recomienda volver a realizar la prueba.
