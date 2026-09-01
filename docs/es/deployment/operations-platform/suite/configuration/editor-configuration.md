# Configuración del Editor

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Instrucciones Manuales

Este manual introduce la función "Configuración del Editor" de ShimoDocs Suite, adecuada para administradores del sistema y ejecutores que usan esta función por primera vez. Puede seguir los pasos en este documento para encontrar elementos de configuración, modificar interruptores o cuotas de funciones, verificar si surten efecto y restaurar la configuración original si es necesario.

**Regla de Alcance Más Importante** Cuando el ID del equipo se deja en blanco, se consulta y modifica la configuración de aplicación predeterminada; cuando se ingresa un ID de equipo, se consulta y modifica la configuración del equipo correspondiente. Modificar la configuración de aplicación predeterminada puede afectar a múltiples equipos, por lo que confirme nuevamente el alcance de la configuración antes de guardar.

### 1.1 Entrada de Acceso

Backend de Administración > ShimoDocs Suite > Gestión de Configuración > Configuración del Editor

### 1.2 Preparativos Antes de Usar

- Confirma que tu cuenta de inicio de sesión tiene permiso para ver y modificar ShimoDocs Suite las configuraciones del editor.
- Primero confirma si el ámbito objetivo es la aplicación predeterminada o un equipo específico, y obtiene el ID de equipo exacto.
- Confirma los nombres de los elementos de configuración a partir de los requisitos de configuración o del Apéndice A. Los nombres de los elementos de configuración son identificadores únicos; no adivines solo basándote en los nombres de las funciones en chino.
- Registra la fuente, los valores efectivos y el estado de restricción antes de la modificación; para configuraciones importantes, también prepara los valores de reversión.
- Las configuraciones de la aplicación predeterminada tienen un impacto amplio; se recomienda modificarlas durante periodos de baja actividad y notificar previamente a las personas responsables correspondientes.

## 2. Ámbito y Prioridad de Configuración

La configuración del editor soporta dos ámbitos: Predeterminado de la App y Equipo. Antes de hacer cambios, debes verificar tanto el ID del equipo como la 'Dimensión Actual' en la parte superior de la página.

| **Área Funcional** | **Ámbito Predeterminado de la App** | **Ámbito del Equipo** | **Indicador de Reconocimiento de Página** |
| --- | --- | --- | --- |
| Configuración del Editor | Deja el ID del equipo en blanco | Ingresa un ID de equipo entero positivo | La Dimensión Actual muestra 'Predeterminado de la App' o 'Equipo' |

*Figura 1 Correspondencia entre el ID del Equipo y la Dimensión Actual*

### 2.1 Ámbito Predeterminado de la Aplicación

- Mantén el ID del equipo vacío.
- La parte superior de la página muestra “Dimensión Actual: Predeterminado de la Aplicación.”
- El valor predeterminado de la aplicación es el valor base cuando no se establece ninguna anulación de equipo; modificarlo puede afectar a varios equipos.
- Antes de guardar, confirme nuevamente que el ID del Equipo esté efectivamente vacío para evitar escribir por error los requisitos del equipo como configuraciones predeterminadas de la aplicación.

### 2.2 Alcance del Equipo

- Ingrese el ID del equipo objetivo, un número entero positivo, en el campo ID del Equipo y luego haga clic en “Consultar”.
- La parte superior de la página muestra “Dimensión Actual: Equipo.”
- La configuración a nivel de equipo solo afecta al equipo correspondiente al ID de Equipo ingresado y no cambia directamente la configuración de otros equipos.
- Cuando un elemento de configuración tiene una configuración a nivel de equipo, el valor efectivo del equipo tiene prioridad sobre el valor predeterminado de la aplicación.

### 2.3 Anulación, Herencia y Restauración

- Si el equipo actual no tiene una anulación personalizada, se utiliza la configuración predeterminada de la aplicación o el valor predeterminado del sistema.
- La “Fuente” en la lista puede ayudar a determinar si el valor actual proviene del valor predeterminado del sistema, la anulación de la aplicación o la anulación del equipo.
- Después de eliminar la anulación de la capa actual, la configuración generalmente vuelve a heredar el valor de nivel superior; confirme el resultado después de la herencia antes de la eliminación.
- Después de modificar o restaurar, consulte nuevamente el mismo ID de Equipo y elemento de configuración para confirmar que la fuente y el valor efectivo cumplan con las expectativas.

**Advertencia de Riesgo** No guarde directamente sin confirmar la dimensión actual. Cuando el ID del Equipo esté vacío, las operaciones se registrarán en el ámbito predeterminado de la aplicación, lo que puede afectar a varios equipos. 

## 3. Configuración del Editor 

La configuración del editor se utiliza para ver y ajustar los interruptores de funciones, las cuotas de uso y la configuración estructurada del ShimoDocs Suite editor. Puede filtrar por tipo o expandir el "Filtro Avanzado" e ingresar el nombre del elemento de configuración en la "Lista blanca de nombres" para una búsqueda precisa. 

### 3.1 Campos de la Página 

| **Campo** | **Descripción** | 
| --- | --- | 
| ID de la Aplicación | Actual ShimoDocs Suite Identificador de la aplicación, usado solo para confirmar el contexto. | 
| Dimensión Actual | "Predeterminado de la Aplicación" cuando el ID del Equipo está vacío; "Equipo" después de que se complete el ID del Equipo. | 
| ID del Equipo | Identificador del equipo; solo acepta enteros positivos. | 
| Tipo | Las opciones incluyen Todos, Función, Cuota de Valor Único, Cuota de Rango, o JSON Configuración. | 
| Filtro Avanzado | Expanda la caja de entrada para los nombres de los elementos de configuración. | 
| Lista Blanca de Nombres | Ingrese los nombres de los elementos de configuración; admite uno por línea o separados por comas en inglés. | 
| Fuente | Indica si el valor actual proviene de la configuración predeterminada del sistema, de una anulación de la aplicación o de una anulación del equipo. | 
| Valor Efectivo | El interruptor, cuota o configuración estructurada real usada en el alcance actual. | 
| Acción | Icono de lápiz para editar; icono de eliminar para quitar la anulación de la capa actual. | 

*Figura 2 Área de Consulta de Configuración del Editor, Lista de Resultados y Columna de Acciones*

### 3.2 Búsqueda Precisa

1. Decida si llenar el ID del Equipo según el rango de configuración: dejar en blanco para el rango predeterminado de la aplicación o completar para el equipo correspondiente.
2. Seleccione "Tipo" según sea necesario; si no está seguro del tipo, mantenga "Todos".
3. Haga clic en "Filtro Avanzado" para expandir la "Lista Blanca de Nombres".
4. Ingrese el nombre completo del elemento de configuración. Para varios nombres, ingrese uno por línea o sepárelos con comas.
5. Haga clic en "Consultar" para verificar los nombres, tipos, fuentes y valores efectivos en los resultados.
6. Haga clic en el ícono del lápiz en el extremo derecho de la fila objetivo para abrir la ventana emergente de edición.

*Figura 3 Complete la lista blanca de nombres después de hacer clic en 'Filtro Avanzado'*

*Figura 4 Un solo resultado después de la búsqueda precisa para editar_límite_mosheet_tamaño*

**Consejos de Operación** Si no ve los íconos de acción, desplácese horizontalmente hasta el extremo derecho de la lista o amplíe el área visible del navegador.

### 3.3 Búsqueda Directa en la Lista

- Primero confirme que los filtros de ID del Equipo y de tipo sean correctos, luego recorra los resultados de la consulta.
- Use tanto “Nombre” como “Tipo” para confirmar la configuración objetivo; no dependa únicamente del valor actual.
- El número de registros que se muestran en la página puede cambiar dependiendo de la versión de despliegue y de los elementos de configuración que admite la aplicación actual.
- Después de encontrar la fila objetivo, haga clic en el ícono del lápiz en el extremo derecho para entrar en modo de edición.

### 3.4 Edición de la Configuración

#### 3.4.1 Función

El tipo de función se usa para controlar si una capacidad está disponible. Después de abrir la ventana emergente de edición, seleccione el estado proporcionado en la página como “Soporte” o “Ocultar” del menú desplegable “Valor Efectivo”, luego haga clic en “Guardar”. Algunos nombres de elementos de configuración incluyen semánticas inversas como no soportar o desactivar, así que por favor juzgue el significado real según el nombre del elemento y la descripción.

*Figura 5 Configuración de Valor Efectivo de los Elementos de Configuración por Tipo de Función*

#### 3.4.2 Cuota de Valor Único

Una cuota de valor único normalmente incluye un interruptor de "verificación de límite" y un "valor máximo". Cuando la verificación de límite está habilitada, el sistema validará según el valor máximo; cuando está deshabilitada, usualmente se muestra como "ilimitado". El valor máximo debe estar dentro del rango permitido del parámetro y ser consistente con la unidad en el nombre del parámetro, como MB, GB, páginas, elementos o caracteres.

*Figura 6 Validación de Cuota de Valor Único y Configuración del Valor Máximo*

#### 3.4.3 Cuota de Rango

- Las cuotas de rango usualmente proporcionan tanto un valor mínimo como un valor máximo.
- El valor mínimo no puede ser mayor que el valor máximo, y el valor ingresado debe estar dentro del rango permitido indicado en la página o en el apéndice.
- Si la página proporciona una opción de "Sin Validación" o "Sin Límite", primero confirme si la función actual admite esta configuración.
- Después de guardar, verifique los valores límite en la función empresarial real para evitar verificar solo la visualización en el backend de configuración.

#### 3.4.4 JSON Configuración

- JSON La configuración debe mantener una estructura válida, incluyendo comillas, comas, corchetes pareados y tipos de datos correctos.
- Guarde el valor original completo antes de realizar cambios; no registre solo un campo.
- Cuando el significado de un campo no esté claro, no agregue, elimine ni renombre campos de manera arbitraria.

### 3.5 Guardar y Eliminar

- Antes de guardar, vuelva a confirmar la dimensión actual, ID de equipo, nombre del elemento de configuración, tipo, unidad y nuevo valor.
- Después de guardar, consulte nuevamente el mismo rango y el mismo elemento de configuración para confirmar que los valores de origen y efectivos se hayan actualizado.
- El icono de eliminar generalmente se utiliza para quitar el registro de anulación del rango actual, no para eliminar el elemento de configuración mismo.
- Este manual solo enumera los elementos de configuración que se pueden consultar y modificar en la página actual; los elementos que se muestran realmente pueden variar según la versión de despliegue y las capacidades de soporte de la aplicación actuales.

### 3.6 Descripción del Elemento de Configuración

El Apéndice A solo incluye los elementos de configuración del editor que se pueden consultar y modificar en la página actual; el rango visible específico está sujeto a la versión de despliegue actual y a la visualización real de la página.

## 4. Verificación de Efectos y Reversión

### 4.1 Verificación Después de Guardar

- En la página de configuración del editor, vuelva a consultar el mismo ID de equipo y el mismo elemento de configuración para confirmar el valor de origen, el valor efectivo y el estado de restricción.
- Ingrese a la página del editor o de la función que realmente utiliza esta configuración para verificar si la función es visible, si la cuota es efectiva o si la restricción se ha levantado.
- Al aplicar la configuración predeterminada, verifique con al menos un equipo que no tenga configuraciones a nivel de equipo establecidas; para configuraciones a nivel de equipo, solo verifique el ID de equipo objetivo.
- Actualice la página, vuelva a ingresar al editor, inicie sesión nuevamente o espere la actualización de la caché si es necesario.
- Registre la hora de modificación, el operador, el alcance de configuración, el ID de equipo, el nombre del elemento de configuración, los valores antes y después de la modificación y los resultados de la verificación.

### 4.2 Reversión

- Si se ha registrado el valor original, edítelo nuevamente y vuelva a escribir el valor original.
- Si solo se necesita eliminar la anulación del alcance actual, use el ícono de eliminar y confirme el valor heredado del nivel superior después de la eliminación.
- Después de revertir, vuelva a consultar el valor de origen y efectivo, y entre en la página del negocio para verificar nuevamente.
- Si aplicar la modificación de la configuración predeterminada causa anomalías, restaure primero el valor predeterminado y luego verifique si algún equipo tiene anulaciones independientes.

**Nota Importante** Después de eliminar la anulación actual, el valor heredado puede aparecer inmediatamente en la página. Antes de la eliminación, se debe confirmar que la configuración del nivel superior cumple con las expectativas, y se debe conservar un registro del estado previo a la modificación.

## 5. Preguntas Frecuentes

| **Problema** | **Solución** |
| --- | --- |
| No se puede encontrar el cuadro de entrada del nombre del elemento de configuración | Haga clic en “Filtro Avanzado” para expandir la “Lista Blanca de Nombres”. |
| No se puede encontrar el ícono de editar o eliminar | Desplácese horizontalmente hasta el extremo derecho de la lista o amplíe la ventana del navegador. |
| La búsqueda exacta no devuelve resultados | Verifique las mayúsculas del nombre, guiones bajos, el ID del equipo y los filtros de tipo; elimine condiciones de filtro demasiado estrictas e intente buscar nuevamente. |
| Después de ingresar el ID del equipo, aún no está en la dimensión del equipo | El ID del equipo debe ser un número entero positivo válido; después de ingresarlo, haga clic de nuevo en “Consultar” y verifique la “Dimensión Actual” en la parte superior de la página. |
| Después de guardar, la página del negocio no cambia | Verifique si se seleccionó un alcance incorrecto, si está anulado por el equipo, si se necesita actualizar o volver a iniciar sesión, y si el elemento de configuración se aplica a la función actual. |
| El ícono de eliminar no está disponible | El ámbito actual puede no tener reemplazos personalizados y está usando el valor predeterminado del sistema o un valor heredado de un nivel superior. |
| Error al guardar la cuota | Verifique el rango de valores, la unidad, la relación entre los valores mínimo y máximo, y confirme si se permite "Ilimitado". |
| JSON Error al guardar la configuración | Usar válido JSON; revise comillas, comas, corchetes y tipos de campo; si no está seguro, restaure el valor original completo antes de modificar. |

## Apéndice A: Índice de Elementos de Configuración del Editor

El siguiente índice lista solo los elementos de configuración del editor que se pueden consultar y modificar en la página actual; el ámbito visible específico depende de la versión actualmente desplegada.

| **Nombre del Elemento de Configuración** | **Categoría / Descripción de Función** | **Tipo** | **Valor Predeterminado / Rango Opcional** | **Método de Configuración** |
| --- | --- | --- | --- | --- |
| exportar_modoc_docx | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_modoc_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_modoc_pdf | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_modoc_pdf_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_modoc_wps | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_pdf_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_único_hoja_csv | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_único_hoja_pdf_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_único_hoja_xlsx | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_xlsx | Exportar / Tabla | Interruptor de Función | Activado | Configurable en Página |
| exportar_mosheet_zip | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_presentación_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_presentación_pdf | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_presentación_pdf_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_presentación_pptx | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_rdoc_docx | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_rdoc_img | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_rdoc_md | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_rdoc_pdf | Exportar | Interruptor de Función | Activado | Configurable en Página |
| exportar_tabla_xlsx | Exportar / Tabla de Aplicaciones | Interruptor de Función | Activado | Configurable en Página |
| formulario_notificación | Edición de Formulario / Configurar Alertas de Notificación (Alertas de Respuesta, Actualizaciones de Suscripción) | Interruptor de Función | Activado | Configurable en Página |
| importar_convertir_svg | Importar / Subir / Tipo de Archivo de Conversión Forzada de Adjuntos | Interruptor de Función | Activado | Configurable en Página |
| importar_mapa mental_xmind | Importar/Subir / Mapa Mental | Interruptor de Función | Activado | Configurable en Página |
| importar_modoc_doc | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_modoc_docx | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_modoc_wps | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_modoc_wpt | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_mosheet_csv | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_mosheet_xls | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_mosheet_xlsm | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_mosheet_xlsx | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_presentación_ppt | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_presentación_pptx | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_rdoc_doc | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_rdoc_docx | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_rdoc_md | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_rdoc_txt | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_tabla_csv | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_tabla_xls | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_tabla_xlsx | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_no compatible_adjunto_svg | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| importar_no compatible_adjunto_xml | Importar/Subir | Interruptor de Función | Activado | Configurable en Página |
| mosheet_combinar_hojas | Edición de hojas de cálculo / Combinar hojas | Interruptor de Función | Oculto/Apagado | Configurable en Página |
| mosheet_fecha_mencionar | Edición de hojas de cálculo / Recordatorio de fecha | Interruptor de Función | Activado | Configurable en Página |
| mosheet_seguir_modo | Edición de hojas de cálculo / Modo de seguimiento | Interruptor de Función | Activado | Configurable en Página |
| mosheet_seguir_selección | Edición de hojas de cálculo / Seguir selección | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| mosheet_importar_rango | Edición de hojas de cálculo / Referencia entre hojas | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| mosheet_independiente_vista | Edición de hojas de cálculo / Vista independiente | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| presentación_remoto_demo | Edición de diapositivas / Presentación remota | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| previsualizar_no compatible_ofd | Previsualizar | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| previsualizar_no compatible_pdf | Previsualizar | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| previsualizar_no compatible_rtf | Previsualizar / Texto (La previsualización no soporta RTF) | Interruptor de Función | Ocultar/Cerrar | Configurable en Página |
| rdoc_seguir_modo | Edición de documentos / Modo de seguimiento | Interruptor de Función | Activado | Configurable en Página |
| rdoc_notificación | Edición de documentos / Alertas de notificación | Interruptor de Función | Activado | Configurable en Página |
| rdoc_ancho_papel | Edición de documentos / Papel ancho | Interruptor de Función | Activado | Configurable en Página |
| sdk_editor_acerca de_marca_visible | Entrada de marca del editor | Interruptor de Función | Activado | Configurable en Página |
| sdk_editor_acerca de_entrada_visible | Entrada de marca del editor | Interruptor de Función | Activado | Configurable en Página |
| sdk_editor_oficial_sitio web_entrada_visible | Entrada de marca del editor | Interruptor de Función | Activado | Configurable en Página |
| tabla_asociación_referencia_o_fórmula | Edición de tablas de aplicaciones / Campo - Referencia asociada y Fórmula asociada | Interruptor de Función | Oculto/Apagado | Configurable en Página |
| tabla_notificación | Edición de tablas de aplicaciones / Recordatorio de fecha | Interruptor de Función | Activado | Configurable en Página |
| tabla_referir_datos | Edición de tablas de aplicaciones / Tabla de datos de referencia (Hojas combinadas) | Interruptor de Función | Oculto/Apagado | Configurable en Página |
| subir_imagen_gif | Formato de imagen para subir | Interruptor de Función | Activado | Configurable en Página |
| subir_imagen_jpeg | Formato de imagen para subir | Interruptor de Función | Activado | Configurable en Página |
| subir_imagen_png | Formato de imagen para subir | Interruptor de Función | Activado | Configurable en Página |
| subir_imagen_tiff | Formato de imagen para subir | Interruptor de Función | Activado | Configurable en Página |
| subir_imagen_webp | Formato de imagen para subir | Interruptor de Función | Activado | Configurable en Página |
| adjuntar_límite_todo_img_tamaño | Parámetro de adjuntos / Tamaño máximo de imágenes subidas (MB) | Cuota | Predeterminado 512; 0–512 | Configurable en Página |
| adjuntar_límite_todo_tamaño | Parámetro de adjuntos / Tamaño máximo de archivos subidos (GB) | Cuota | Predeterminado 2048; 0–2048 | Configurable en Página |
| editar_límite_formulario_tamaño | Editar Parámetro / Tamaño Máximo de Datos Editables (MB) | Cuota | Predeterminado 100; 0–100 | Configurable en Página |
| editar_límite_formulario_enviar | Editar Parámetro / Número Máximo de Envíos por Formulario | Cuota | Predeterminado 50000; 0–50000 | Configurable en Página |
| editar_límite_modoc_tamaño | Editar Parámetro / Tamaño Máximo de Datos Editables (MB) | Cuota | Predeterminado 100; 0–100 | Configurable en Página |
| editar_límite_mosheet_calcular_celdas | Editar Parámetro / Fórmula - Referencia Cruzada de Hojas - Número Máximo de Celdas Referenciadas | Cuota | Predeterminado 1500000; 0–1500000; No Verificado | Configurable en Página |
| editar_límite_mosheet_calcular_complejidad | Editar Parámetro / Fórmula - Referencia Cruzada de Hojas - Complejidad de las Fórmulas Referenciadas | Cuota | Predeterminado 6000000; 0–6000000; No Verificado | Configurable en Página |
| editar_límite_mosheet_función_referencia | Editar Parámetros / Fórmula - Número máximo de funciones de referencia cruzada de hojas que se pueden ingresar (unidades) | Cuota | Predeterminado 4000; 0–4000 | Configurables en página |
| editar_límite_mosheet_hoja_celda | Editar Parámetros / Número máximo de celdas en una sola hoja de cálculo | Cuota | Predeterminado 0; 0–0; No validado | Configurables en página |
| editar_límite_mosheet_hoja_fc | Editar Parámetros / Número máximo de fórmulas que se pueden ingresar en una sola hoja de cálculo | Cuota | Predeterminado 0; 0–0; No validado | Configurables en página |
| editar_límite_mosheet_tamaño | Editar Parámetros / Volumen máximo de datos editables (MB) | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| editar_límite_mosheet_vista | Editar Parámetros / Número máximo de vistas separadas que un usuario puede crear en una sola hoja de cálculo (unidades) | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| editar_límite_presentación_página | Editar Parámetros / Número de diapositivas | Cuota | Predeterminado 2000; 0–2000 | Configurables en página |
| editar_límite_presentación_tamaño | Editar Parámetros / Volumen máximo de datos editables (MB) | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| editar_límite_rdoc_tamaño | Editar Parámetros / Volumen máximo de datos editables (MB) | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| editar_límite_tabla_calendario_vista | Editar parámetro / Número máximo de vistas de calendario por un solo archivo | Cuota | Predeterminado 200; 0–200 | Configurables en página |
| editar_límite_tabla_contar | Editar parámetro / Número máximo de tablas de datos | Cuota | Predeterminado 200; 0–200 | Configurables en página |
| editar_límite_tabla_gantt_vista | Editar parámetro / Número máximo de vistas Gantt por un solo archivo | Cuota | Predeterminado 200; 0–200 | Configurables en página |
| editar_límite_tabla_bloquear_vista | Editar parámetro / Número máximo de vistas de bloqueo por una sola tabla de datos | Cuota | Predeterminado 50; 0–50 | Configurables en página |
| editar_límite_tabla_manual_versión | Editar parámetro / Número de versiones guardadas manualmente | Cuota | Predeterminado 10000; 0–10000 | Configurables en página |
| editar_límite_tabla_combinar_tabla_referencia | Editar parámetro / Número máximo de tablas de datos a las que puede hacer referencia una sola hoja de cálculo combinada | Cuota | Predeterminado 20; 0–20 | Configurables en página |
| editar_límite_tabla_combinar_tabla_resumen | Editar parámetro / Número máximo de hojas de cálculo combinadas | Cuota | Predeterminado 20; 0–20 | Configurables en página |
| editar_límite_tabla_personal_vista | Editar parámetro / Número máximo de vistas personales por cada tabla de datos | Cuota | Predeterminado 50; 0–50 | Configurables en página |
| editar_límite_tabla_único_col | Editar parámetro / Número total de columnas de una sola tabla de datos | Cuota | Predeterminado 50; 0–50 | Configurable en Página |
| editar_límite_tabla_único_fila | Editar parámetro / Número total de filas de una sola tabla de datos | Cuota | Predeterminado 20000; 0–20000 | Configurable en Página |
| editar_límite_tabla_único_vista | Editar parámetro / Número máximo de vistas de una sola tabla de datos | Cuota | Predeterminado 200; 0–200 | Configurable en Página |
| editar_límite_tabla_tamaño | Editar parámetro | Cuota | Predeterminado 100; 0–100 | Configurable en Página |
| exportar_límite_rdoc_pixel_altura | Parámetro de exportación / Altura máxima de la imagen exportada (px) | Cuota | Predeterminado 66000; 0–66000 | Configurable en Página |
| exportar_tamaño_límite | Parámetro de exportación / Tamaño máximo del archivo exportado (GB) | Cuota | Predeterminado 3072; 0–3072 | Configurable en Página |
| historial_límite_todo_tiempo | Parámetro de historial / Días de retención del historial de archivos | Cuota | Predeterminado 10000000000000000; 0–10000000000000000; No validado | Configurable en Página |
| historial_límite_mosheet_celda_tiempo | Parámetro de historial / Días de retención del historial de celdas de la tabla | Cuota | Predeterminado 10000000000000000; 0–10000000000000000; No validado | Configurable en Página |
| historial_límite_revertir_num | Parámetro de historial / Número de registros de historial recientes que se pueden restaurar para un solo archivo | Cuota | Predeterminado 2000; 0–2000 | Configurables en página |
| historial_límite_tabla_celda_tiempo | Parámetro de historial / Número de días para retener el historial de celdas de tabla de la aplicación | Cuota | Predeterminado 10000000000000000; 0–10000000000000000; no validado | Configurables en página |
| historial_límite_tabla_fila_tiempo | Parámetro de historial / Número de días para retener historial dinámico de filas de tabla de la aplicación | Cuota | Predeterminado 10000000000000000; 0–10000000000000000; no validado | Configurables en página |
| historial_límite_versión_num | Parámetro de historial / Número de versiones (instantáneas) que se pueden guardar/restaurar para un solo archivo | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| importar_exportar_tiempo de espera | Parámetro de importación / Tiempo máximo de importación (min) | Cuota | Predeterminado 10; 0–10 | Configurables en página |
| importar_límite_modoc_tamaño | Parámetro de importación / Tamaño máximo del archivo (MB) | Cuota | Predeterminado 300; 0–300 | Configurables en página |
| importar_límite_modoc_palabra | Parámetro de importación / Número máximo de caracteres (Carácter) | Cuota | Predeterminado 2000000; 0–2000000 | Configurables en página |
| importar_límite_mosheet_todo_hoja_celda | Parámetro de importación / Número máximo de celdas válidas en una hoja | Cuota | Predeterminado 5,000,000; 0–5,000,000 | Configurable en Página |
| importar_límite_mosheet_todo_xml_tamaño | Parámetro de importación / Tamaño total de todos XML los archivos en la hoja (MB) | Cuota | Predeterminado 300; 0–300 | Configurable en Página |
| importar_límite_mosheet_convertido_tamaño | Parámetro de importación / ShimoDocs Volumen de datos (MB) | Cuota | Predeterminado 100; 0–100 | Configurable en Página |
| importar_límite_mosheet_único_hoja_celda | Parámetro de importación / Número máximo de celdas válidas en una sola hoja de cálculo | Cuota | Predeterminado 2,000,000; 0–2,000,000 | Configurable en Página |
| importar_límite_mosheet_único_xml_tamaño | Parámetro de importación / Tamaño máximo de un solo XML Archivo en la hoja (MB) | Cuota | Predeterminado 20; 0–20 | Configurable en Página |
| importar_límite_mosheet_tamaño | Parámetro de importación / Tamaño máximo de archivo (MB) | Cuota | Predeterminado 300; 0–300 | Configurable en Página |
| importar_límite_presentación_página | Parámetro de importación / Número máximo de diapositivas (Páginas) | Cuota | Predeterminado 2000; 0–2000 | Configurable en Página |
| importar_límite_presentación_tamaño | Parámetro de importación / Tamaño máximo de archivo (MB) | Cuota | Predeterminado 100; 0–100 | Configurables en página |
| importar_límite_rdoc_tamaño | Parámetro de importación / Tamaño máximo de archivo (MB) | Cuota | Predeterminado 50; 0–50 | Configurables en página |
| importar_límite_rdoc_palabra | Parámetro de importación / Número máximo de caracteres (Carácter) | Cuota | Predeterminado 300000; 0–300000 | Configurables en página |
| importar_límite_tabla_único_col | Parámetro de importación / Número máximo de columnas efectivas por hoja de cálculo (Columnas) | Cuota | Predeterminado 50; 0–50 | Configurables en página |
| importar_límite_tabla_único_fila | Parámetro de importación / Número máximo de filas efectivas por hoja de cálculo (Filas) | Cuota | Predeterminado 20000; 0–20000 | Configurables en página |
| pegar_límite | Parámetro de pegar / Volumen máximo de datos por pegado (MB) | Cuota | Predeterminado 9; 0–9 | Configurables en página |
| pegar_límite_modoc | Parámetro de pegar / Número máximo de caracteres por pegado (Carácter) | Cuota | Predeterminado 200000; 0–200000 | Configurables en página |
| pegar_límite_mosheet | Parámetro de pegar / Número máximo de celdas por pegado (Unidades) | Cuota | Predeterminado 2000000; 0–2000000 | Configurables en página |
| pegar_límite_presentación | Parámetros de pegar / Número máximo de diapositivas que se pueden pegar a la vez | Cuota | Predeterminado 200; 0–200 | Configurable en la página |
| pegar_límite_rdoc | Parámetros de pegar / Número máximo de caracteres que se pueden pegar a la vez | Cuota | Predeterminado 200000; 0–200000 | Configurable en la página |
| pegar_límite_tabla | Parámetros de pegar / Número máximo de filas que se pueden pegar a la vez | Cuota | Predeterminado 2000; 0–2000 | Configurable en la página |
| previsualizar_tiempo de espera | Parámetros de vista previa / Tiempo máximo de vista previa (min) | Cuota | Predeterminado 10; 0–10 | Configurable en la página |

## Apéndice B: Terminología y Correspondencia de Campos de Página

| **Término** | **Significado** |
| --- | --- |
| Nombre del Elemento de Configuración / Lista Blanca de Nombres | El nombre único del elemento de configuración, por ejemplo, rdoc_notificación, editar_límite_mosheet_tamaño. |
| ID del Equipo | Identificador del equipo; ingrese un número entero positivo para establecer el alcance de configuración a nivel de equipo. |
| Predeterminado de la Aplicación | El alcance de configuración cuando el ID del equipo se deja en blanco; referido en este manual como configuración global. |
| Configuración a Nivel de Equipo | Anula configuraciones que son efectivas solo para el ID de equipo especificado. |
| Predeterminado del Sistema | Cuando no hay una anulación en este nivel, se utiliza el valor predeterminado incorporado del producto. |
| Cobertura de Aplicación / Cobertura de Equipo | Existen configuraciones personalizadas en la capa actual, que tienen prioridad sobre los valores de niveles superiores. |
| Interruptor de Función | Parámetro de tipo interruptor o estado. |
| Cuota de Valor Único | Un valor máximo y un interruptor de validación de límite opcional. |
| Cuota de Rango | Un parámetro de rango que incluye tanto valores mínimos como máximos. |
| JSON Configuración | Parámetro estructurado que debe permanecer válido JSON; algunos elementos de configuración no se muestran en la página actual. |
| Sin Validación / Sin Límite | No realiza validación de límite según el máximo ingresado. |
