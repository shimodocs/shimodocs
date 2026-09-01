# Configuración del Sistema

[← ShimoDocs Suite Documentación de implementación](../../../README.md)

## 1. Instrucciones

Este manual presenta la función "Configuración del Sistema" de ShimoDocs Suite, adecuada para administradores del sistema e implementadores que utilizan esta función por primera vez. Puede seguir los pasos en este documento para encontrar elementos de configuración, modificar configuraciones, verificar si los cambios surten efecto y restaurar la configuración original si es necesario.

> [!TIP]
>
> Si no está seguro sobre el significado o impacto de un elemento de configuración, por favor contacte a ShimoDocs soporte técnico para confirmación antes de realizar cualquier cambio.

**Regla de Alcance Más Importante** Cuando el ID de la empresa se deja en blanco, la consulta y la modificación se aplican a la configuración global; cuando se selecciona un ID de empresa, la consulta y la modificación se aplican a la configuración de la empresa seleccionada. Modificar la configuración global puede afectar a múltiples empresas, por lo que se debe reconfirmar el alcance de la configuración antes de guardar.

### 1.1 Entrada de Acceso

Backend de Administración > ShimoDocs Suite > Gestión de Configuración > Configuración del Sistema

### 1.2 Preparación Antes de Usar

- Confirme que la cuenta de inicio de sesión tiene permisos de visualización y edición para la ShimoDocs Suite configuración del sistema.
- Primero confirme si el alcance objetivo es global o de una empresa específica, y obtenga el ID de empresa exacto.
- Confirme los nombres de las claves de configuración a partir de los requisitos de configuración o del Apéndice A. El nombre de la clave de configuración es un identificador único; por favor, no haga suposiciones basadas únicamente en el nombre en chino.
- Registre la fuente, el estado y el valor efectivo antes de la modificación; para configuraciones importantes, también prepare los valores de reversión.
- Las configuraciones globales con gran impacto deben modificarse durante períodos de menor actividad comercial, y el personal relevante debe ser informado con antelación.

## 2. Ámbito y Prioridad de Configuración

La configuración del sistema admite tanto alcance global como empresarial. Antes de la modificación, se deben verificar el campo de ID de empresa y la indicación de alcance en la parte inferior de la página.

| **Área Funcional** | **Alcance Global** | **Alcance Empresarial** | **Señal de Identificación de Página** |
| --- | --- | --- | --- |
| Configuración del Sistema | Dejar en blanco el ID de empresa | Seleccionar ID de empresa | Indicación en la parte inferior «Anulación de versión global» o «Resultado final efectivo de la empresa» |

*Figura 1 Ubicación para seleccionar el ID de la Empresa en la configuración del sistema*

### 2.1 Configuración Global

- Mantener el ID de la Empresa sin seleccionar.
- La página indica que la consulta y modificación actual son para la 'Anulación de Versión Global'.
- Los valores globales son los valores base utilizados cuando no se establece una anulación de empresa; modificarlos puede afectar a varias empresas.
- Antes de guardar, verifique al menos una vez más para asegurarse de que el campo ID de la Empresa esté realmente vacío.

### 2.2 Configuración a Nivel de Empresa

- Seleccione la empresa objetivo del menú desplegable de ID de la Empresa.
- La página muestra el resultado final efectivo después de combinar los valores predeterminados con la configuración personalizada de la empresa actual.
- La configuración a nivel de empresa solo afecta a la empresa seleccionada y no cambia directamente la configuración de otras empresas.
- Cuando existe una configuración a nivel de empresa para el mismo elemento, el valor final efectivo de la empresa tiene prioridad sobre el valor global.

### 2.3 Anulación, Herencia y Restauración

- Cuando la empresa actual no tiene anulación, se utilizan los valores de configuración global o los valores predeterminados del sistema.
- Operaciones como 'Restaurar Predeterminado del Sistema' o eliminar la anulación actual generalmente significan eliminar la anulación del ámbito actual y volver a heredar el valor del nivel superior.
- Los avisos en la página como 'Predeterminado del Sistema', 'Anulación de Versión Global' y 'Resultado Final Efectivo de la Empresa' se pueden usar para determinar de qué capa proviene el valor actual.
- Antes de realizar la restauración o eliminación, registre el valor actual y confirme que el resultado heredado cumpla con las expectativas.

**Advertencia de Riesgo** No guarde directamente sin confirmar el alcance de la empresa. Si el ID de la empresa está vacío, la operación puede escribir una anulación global y afectar a varias empresas.

## 3. Configuración del Sistema

La configuración del sistema se utiliza para ver y ajustar las funciones generales, cuotas y parámetros de operación de ShimoDocs Suite.

### 3.1 Método de Búsqueda Uno: Búsqueda Exacta

- ID de la empresa: Déjelo vacío para global; seleccione un ID de empresa para configuración a nivel empresarial.
- Criterios de búsqueda: seleccione el tipo, tipo de valor y fecha de finalización según sea necesario; mantenga "Todos" si no está seguro.
- Nombre de la Clave: ingrese el nombre de la clave de configuración; una clave por línea o use comas para separar múltiples claves.
- Haga clic en "Buscar" para confirmar el nombre, la clave, la fuente, el estado y el valor actual en los resultados.
- Haga clic en "Editar" en el extremo derecho de la fila objetivo para abrir la ventana emergente de modificación.

*Figura 2  Área de Búsqueda Precisa de Configuración del Sistema*

*Figura 3  Resultado único después de la búsqueda precisa por nombre de clave de configuración*

**Sugerencia de Alcance** Cuando aparece "Cuando no se selecciona ninguna empresa, la consulta y modificación actuales son para la sustitución de la versión global" en la parte inferior de la página, indica que el alcance actual es global. Tras seleccionar una empresa, la página muestra el resultado efectivo final combinando los valores predeterminados con la configuración personalizada de la empresa actual.

### 3.2 Segundo Método de Búsqueda: Ubicar directamente en la lista

- Mantenga el alcance de la empresa y las condiciones de filtro correctas, y use el desplazamiento de la página para recorrer la lista.
- Confirme la configuración objetivo por "Nombre" o "Nombre de Clave", no juzgue únicamente basado en el valor actual.
- Vea el tipo, fecha de finalización efectiva, valor actual, fuente y estado en la misma fila.
- Haga clic en "Editar" en el extremo derecho. Si no puede ver la columna de operaciones, desplace la tabla horizontalmente hacia la derecha o amplíe la ventana del navegador.

### 3.3 Edición de Diferentes Tipos de Configuraciones del Sistema

#### 3.3.1 Tipo Clave-Valor

La parte superior del popup de edición muestra metadatos de solo lectura, incluyendo nombre de clave, nombre, descripción, tipo y fecha de finalización efectiva. Cuando esté habilitado, complete los valores en los cuadros de entrada como "Valor de Cadena" y guarde. Si la cadena contiene JSON, URL, ruta o lista, se debe mantener el formato original.

*Figura 4  Popup de Edición de Tipo de Valor de Clave de Configuración del Sistema*

#### 3.3.2 Tipo Cuota

El popup de cuota generalmente incluye Estado, Valor Mínimo, Valor Máximo y un interruptor de "Sin Validación". Después de habilitar la configuración, rellene el rango de acuerdo con los requisitos del negocio; activar "Sin Validación" significa que el sistema no realiza comprobaciones de restricción según el rango ingresado. Los valores deben ser consistentes con las unidades del popup, como "piezas", "MB", etc.

*Figura 5 Popup de Edición de Tipo Cuota de Configuración del Sistema*

#### 3.3.3 Tipos de Función

El tipo de función se basa principalmente en un interruptor de estado. Activarlo indica que el elemento de configuración está habilitado en el alcance actual; desactivarlo indica que está deshabilitado o no habilitado. Algunas claves tienen semántica inversa y deben determinarse de acuerdo con el nombre y la descripción del elemento de configuración. Por ejemplo, las claves con nombres que contienen 'unsupport' o 'disable' pueden representar 'no compatible' o 'deshabilitado' cuando están activadas.

### 3.4 Guardar, Eliminar y Restaurar

- Antes de guardar, reconfirme el alcance de la empresa, el nombre de la clave, el tipo, la unidad y los valores modificados.
- Después de guardar, busque nuevamente el mismo elemento de configuración para confirmar que la fuente, el estado y el valor efectivo han cambiado.
- Cuando hay una anulación en el alcance actual, la operación 'Eliminar' puede estar disponible; después de eliminar la anulación, volverá al valor heredado del nivel anterior.
- Cuando se necesite una reversión, primero escriba de nuevo el valor registrado originalmente, o elimine la anulación actual después de confirmar la relación de herencia.
- No entienda 'eliminar' como eliminar el propio elemento de configuración; la eliminación en la página generalmente solo se aplica al registro de anulación en el alcance actual.

## 4. Verificación de Efectividad y Reversión

### 4.1 Verificación Después de Guardar

- En la página de configuración del sistema, vuelva a consultar el mismo alcance de la empresa y el mismo elemento de configuración para confirmar la fuente, el estado y el valor efectivo.
- Vaya a la página funcional que realmente usa esta configuración para verificar el rendimiento de la función, en lugar de solo observar el backend de configuración.
- Para la configuración global, se debe verificar al menos una empresa sin configuración a nivel empresarial; la configuración a nivel empresarial solo se verifica para la empresa objetivo.
- Para la cuenta, permisos o configuraciones relacionadas con la caché, actualice la página, inicie sesión nuevamente o espere a que la caché se actualice si es necesario.
- Registre la hora de modificación, el operador, el alcance empresarial, el nombre clave, los valores antes y después de la modificación, y los resultados de la verificación.

### 4.2 Reversión

- Hay un valor original definido: vuelva a editar y escriba el valor original.
- Solo es necesario eliminar la anulación del rango actual: use 'Restaurar valores predeterminados del sistema' o elimine la anulación actual.
- Después de revertir, consulte nuevamente los valores de origen y efectivos, y vuelva a ingresar a la página de negocio para la verificación.
- Si los cambios globales causan anomalías generalizadas, se debe dar prioridad a restaurar la cobertura global, y luego investigar las diferencias en la cobertura entre empresas individuales.

**Nota Importante** Las eliminaciones en la página generalmente se aplican a los registros de anulación dentro del alcance actual; el elemento de configuración en sí aún existe. Es necesario confirmar que el valor heredado cumple con las expectativas antes de la eliminación.

## 5. Preguntas Frecuentes

| **Pregunta** | **Método de manejo** |
| --- | --- |
| No se puede encontrar el botón de editar o de acción | Cuando la tabla es ancha, desplácese horizontalmente hacia el extremo derecho; también puede ampliar el área visible del navegador. |
| Sin resultados para la búsqueda exacta | Verifique las mayúsculas y guiones bajos del nombre clave; confirme el rango de ID empresarial; borre filtros demasiado estrictos de tipo, tipo de valor o fin de vigencia. |
| Después de guardar, la página de negocio no tiene cambios | Verifique si se seleccionó un alcance corporativo incorrecto, si la fuente está siendo sobrescrita por la empresa, si se necesita una actualización o volver a iniciar sesión, y confirme si los elementos de configuración son aplicables a la función actual. |
| Botón de restaurar valores predeterminados del sistema no disponible | El alcance actual no tiene sobrescrituras, actualmente se utilizan valores heredados o valores predeterminados del sistema. |
| JSON o URL Error de configuración | Mantener válido JSON o URL formato, no omita comillas, comas o protocolos; verifique primero en la empresa de prueba. |
| El valor efectivo final de la empresa difiere del valor global | La empresa actual puede tener sobrescrituras. Verifique los registros de fuente y sobrescritura para confirmar si se deben mantener las diferencias de la empresa o restaurar la herencia. |

## Apéndice A: Índice de elementos de configuración del sistema

El siguiente índice solo lista los elementos de configuración del sistema que se pueden consultar y modificar en la página actual; el rango visible específico depende de la versión de despliegue actual y la visualización real de la página.

| **Clave de configuración** | **Nombre del Elemento de Configuración** | **Tipo/Regla** | **Soporte de página** |
| --- | --- | --- | --- |
| permitir_equipo_administrador_obtener_invitado_usuario_contraseña | El administrador de la empresa obtiene el inicial PASSWORD de los usuarios invitados | Cadena vacía | Configurables en página |
| automático_inicio de sesión_activado_no_permiso_página | El acceso anónimo sin permiso redirige a la página de inicio de sesión | Cadena vacía | Configurables en página |
| lote_eliminar_archivo_cantidad_límite | Número máximo de archivos para eliminación por lote | 0–500 | Configurables en página |
| lote_descargar_archivos | Número máximo de archivos para descarga por lote único | 0–500 | Configurables en página |
| lote_descargar_tamaño | Tamaño total máximo para descarga por lote único | 0–21474836480 | Configurables en página |
| lote_mover_archivo_cantidad_límite | Número máximo de archivos para mover por lote | 0–500 | Configurables en página |
| marca | Nombre de marca del front-end | Cadena vacía | Configurables en página |
| cambiar_carpeta_colaborador | Colaboración en carpeta | Cadena vacía | Configurables en página |
| clasificación_marcar_configuración_límite | Número máximo de políticas de aprobación de degradación | 0–30 | Configurables en página |
| clasificación_marcar_límite | Número máximo de etiquetas de clasificación | 0–20 | Configurables en página |
| clasificación_marcar_regla_límite | Número máximo de reglas de etiquetas de clasificación | 0–30 | Configurables en página |
| nube_equipo_espacio_descargar_archivo_tamaño | Tamaño máximo de un solo archivo descargado (MB) | 0–3072 | Configurables en página |
| nube_equipo_espacio_subir_archivo_tamaño | Límite de tamaño de carga de archivos en el espacio del equipo | 0–300 | Configurables en página |
| día_desempaquetar_archivo_cantidad_límite | Número máximo de archivos para descomprimir por día | 0–2000 | Configurables en página |
| predeterminado_avatar | Avatar predeterminado URL | Ruta | Configurables en página |
| predeterminado_empresa_papelera_cuota | Cuota predeterminada de la papelera de reciclaje de la empresa | 0–0 | Configurables en página |
| predeterminado_espacio_cuota | Cuota predeterminada del espacio del equipo | 0–107374182400 | Configurables en página |
| predeterminado_equipo_usuario_cuota | Límite de capacidad predeterminado para miembros de la empresa | 0–0 | Configurables en página |
| predeterminado_usuario_archivo_etiquetas | Etiquetas predeterminadas para archivos de usuario | JSON matriz | Configurables en página |
| predeterminado_usuario_cuota | Cuota de espacio personal predeterminada dentro del equipo (Mi escritorio) | 0–107374182400 | Configurables en página |
| departamento_cantidad_límite | Número máximo de departamentos que se pueden crear en la empresa | 0–500 | Configurables en página |
| departamento_profundidad_límite | Número máximo de niveles de departamentos anidados | 0–20 | Configurables en página |
| desactivar_lote_descargar | Desactivar descarga por lotes | Cadena vacía | Configurables en página |
| desactivar_empresa_papelera | Ocultar la papelera de reciclaje de la empresa | Cadena vacía | Configurables en página |
| mostrar_ip_ubicación | Mostrar ubicación de IP | Cadena vacía | Configurables en página |
| unidad_editor_acerca de_marca_visible | Mostrar información de marca en ShimoDocs Suite editor Página Acerca de | Cadena vacía | Configurables en página |
| unidad_editor_acerca de_entrada_visible | Mostrar entrada “Acerca de” en ShimoDocs Suite editor | Cadena vacía | Configurables en página |
| unidad_editor_oficial_sitio web_entrada_visible | ShimoDocs Suite Mostrar entrada del sitio web oficial del editor | Cadena vacía | Configurable en Página |
| activar_enlace_informe | Informe de enlace externo | Cadena vacía | Configurable en Página |
| activar_externo | Colaboradores externos | Cadena vacía | Configurable en Página |
| activar_pc_sistema_tema | activar_pc_sistema_tema | Cadena vacía | Configurable en Página |
| activar_rdoc_md_imagen_exportar_opciones | activar_rdoc_md_imagen_exportar_opciones | Cadena vacía | Configurable en Página |
| activar_riesgos | Identificación de riesgos | Cadena vacía | Configurable en Página |
| activar_compartir_expirar_tiempo | Tiempo de Expiración del Enlace para Compartir | Cadena vacía | Configurable en Página |
| activar_compartir_contraseña | Contraseña de Compartir | Cadena vacía | Configurable en Página |
| archivo_colaborador_límite | Número Máximo de Colaboradores por Archivo | 0–100 | Configurable en Página |
| carpeta_hijos_cantidad_límite | Número Máximo de Archivos al Mismo Nivel | 0–2000 | Configurable en Página |
| gratis_usuario_crear_límite | Límite en el Número de Plantillas que los Usuarios Gratis Pueden Crear | 0–5 | Configurable en Página |
| frontend_tiempo de ejecución_funciones | Lista de elementos de configuración de tiempo de ejecución de Frontend | JSON matriz | Configurables en página |
| importar_usuario_filas_límite | Número máximo de usuarios para importar a la vez | 0–500 | Configurables en página |
| invitar_móvil_límite_caducado | Ventana de tiempo de expiración para el número de invitaciones de colaboración de archivos enviadas por teléfono móvil | 0–3600 | Configurables en página |
| invitar_móvil_límite_máx | Límite en el número de invitaciones de colaboración de archivos vía teléfono móvil | 0–20 | Configurables en página |
| es_abrir_rol_solicitar | Solicitud de permisos de archivo | Cadena vacía | Configurables en página |
| iniciar sesión_dispositivo_límite | Número máximo de dispositivos conectados simultáneamente por cuenta | 0–0 | Configurables en página |
| máx_creador_equipos_por_cuenta | Número máximo de empresas que se pueden crear por cuenta | 0–3 | Configurables en página |
| máx_carpeta_profundidad | Profundidad máxima de anidamiento de carpetas | 0–50 | Configurables en página |
| máx_unido_equipos_por_cuenta | Número máximo de empresas a las que una cuenta puede unirse | 0–100 | Configurables en página |
| máx_papeleras_lista_tamaño | Número de registros devueltos por la interfaz de lista de la papelera de reciclaje | 0–500 | Configurables en página |
| multiparte_subir_habilitar | Carga multipart | Cadena numérica | Configurables en página |
| una vez_desempaquetar_archivo_cantidad_límite | Número máximo de archivos por extracción | 0–500 | Configurables en página |
| solo_propietario_puede_eliminar | Solo el propietario puede eliminar | Cadena vacía | Configurables en página |
| premium_usuario_crear_límite | Número máximo de plantillas que un usuario puede crear | 0–50 | Configurables en página |
| privado_desplegar_página_icono | Configuración del icono de la página | Cadena vacía | Configurables en página |
| público_compartir | Compartir públicamente | Cadena vacía | Configurables en página |
| rag_buscar_regla | RAG reglas de búsqueda | JSON objeto | Configurables en página |
| sdkCheckpointCacheTTL | Duración de la caché de configuración del Editor | 0–600 | Configurables en página |
| sdk_punto de control_lista blanca | Lista blanca de configuración del Editor | JSON objeto | Configurables en página |
| buscar_ia_habilitar | buscar_ia_habilitar | Cadena vacía | Configurables en página |
| compartir_contraseña_longitud | Longitud de la contraseña para compartir | 0–6 | Configurables en página |
| único_archivo_subir_tamaño_límite | Tamaño máximo de un solo archivo cargado (GB) | 0–1 | Configurables en página |
| único_subir_archivo_cantidad_límite | Carga por lotes | Cadena vacía | Configurable en Página |
| equipo_cambiar | Cambio de equipo | Cadena vacía | Configurable en Página |
| equipo_rol_gestionar | Gestión de roles | Cadena vacía | Configurable en Página |
| tema_color | Color del tema del Front-End | Cadena vacía | Configurable en Página |
| tema_color_btn | Color del tema del botón | HEX Valor del color | Configurable en Página |
| ui_radio_config | Configuración de radio del Front-End | Cadena vacía | Configurable en Página |
| subir_lote_máx | Número máximo de archivos por carga | 0–500 | Configurable en Página |

## Apéndice B: Terminología y Correspondencia de Campos de Página

| **Término** | **Significado** |
| --- | --- |
| Clave de configuración / Nombre de la clave | La clave única del elemento de configuración, por ejemplo batch_descargar_archivos. |
| ID de empresa | Identificador de empresa. Seleccionarlo ingresa al alcance de configuración a nivel empresarial. |
| Configuración global | El alcance predeterminado consultado y modificado cuando se deja en blanco el ID de empresa. |
| Configuración a nivel empresarial | Las anulaciones son efectivas solo para la empresa seleccionada. |
| Predeterminado del Sistema | Si no existe una anulación personalizada en el alcance actual, se usa el valor predeterminado incorporado. |
| Anulación de versión global | El elemento de configuración actual tiene configuraciones personalizadas a nivel global. |
| Resultado efectivo de la empresa | El resultado efectivo real después de fusionar el valor predeterminado de la empresa con la anulación de la empresa. |
| Clave-Valor | Un parámetro de valor único almacenado en forma de cadena, que puede contener texto, URL, ruta, o JSON. |
| Cuota | Un rango numérico que incluye valores mínimos, máximos o un interruptor de límite. |
| Función | Un parámetro de tipo interruptor o estado. |
| Sin validación | No realiza verificaciones de validación basadas en el límite superior especificado. |
