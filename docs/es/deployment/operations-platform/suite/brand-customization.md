# Personalización de Marca

[← ShimoDocs Suite Documentación de implementación](../../README.md)

> [!TIP]
>
> La personalización de marca se utiliza para unificar la identidad de la marca y el estilo de la interfaz de ShimoDocs Suite. Aquí puedes configurar tu logotipo corporativo, íconos del navegador, extensiones de marca en las pestañas, colores del tema, esquinas redondeadas de los botones, entrada de la página y marca de agua del sistema. 
>
> Al usar esta función, se recomienda primero confirmar el alcance efectivo de la configuración y luego completar la configuración en el orden de identidad de la marca, estilo de la interfaz, entrada y marca de agua. 

> Cuando no se seleccionan inquilinos, la configuración se aplica globalmente a ShimoDocs Suite. La prioridad para el mismo elemento de configuración es: ** Configuración del inquilino > Configuración global**. 

## 1. Entrar en Personalización de Marca 

1. Inicia sesión en **MDP Plataforma de Operaciones**. 
2. En la parte superior, selecciona **ShimoDocs Suite**. 
3. En la barra de navegación izquierda, selecciona **Personalización de Marca**. 

## 2. Selecciona el rango efectivo de la configuración 

La personalización de marca admite alcance global, de inquilino o de usuario. Antes de configurar, seleccione el rango correspondiente según sus necesidades reales. 

| Rango de configuración | Cómo elegir | Efecto |
| --- | --- | --- |
| Configuración global | No seleccione inquilinos ni usuarios. | Efectivo globalmente para ShimoDocs Suite. |
| Configuración de inquilino | Seleccione el inquilino especificado. | Efectivo solo para los inquilinos seleccionados. |
| Configuración de usuario | Seleccione el usuario especificado. | Efectivo solo para los usuarios seleccionados. |

Por ejemplo: si el color de tema global se establece en azul, y el color de tema de un cierto inquilino se establece en verde, entonces ese inquilino usará verde, mientras que otros inquilinos que no se hayan configurado individualmente seguirán usando azul.

## 3. Configurar identidad de marca

### 1. Logo de la empresa

Al configurar "Modificar logo principal del sitio de la empresa", puede controlar si se muestra la opción para cambiar el logo de la empresa en la **Administración empresarial > Información básica de la empresa** página en ShimoDocs Suite.

Una vez habilitado, los administradores pueden modificar el logo de la empresa en la página de información básica de la empresa.

### 2. Icono del navegador

A través de la configuración "Modificar icono de página del navegador", puede reemplazar el icono (Icono) mostrado por ShimoDocs Suite en la pestaña del navegador.

Después de la configuración, puede ver el efecto real de la visualización en la pestaña del navegador.

### 3. Sufijo de marca en la pestaña del navegador

A través de la configuración "Sufijo de marca en la pestaña del navegador", puede establecer el sufijo del nombre de la marca mostrado por ShimoDocs Suite en la pestaña del navegador.

Después de la configuración, puedes ver el efecto en el título de la pestaña del navegador.

## 4. Estilo de la interfaz de configuración

### 1. Color del tema

A través de la configuración de "Color del tema", puedes ajustar de manera uniforme el color de los botones principales, los estados seleccionados y el contenido resaltado en ShimoDocs Suite.

Después de cambiar el color, puedes previsualizar el efecto real de la aplicación del color del tema en la página.

### 2. Radio de esquina del botón

Ajusta el efecto del radio de las esquinas de los botones en ShimoDocs Suite a través de la "Configuración del radio de esquina".

Después de ajustar los valores, la forma de las esquinas del botón cambiará en consecuencia.

## 5. Configurar entrada de página e información de marca

### 1. Entrada del sitio web oficial

Al configurar "Habilitar entrada del sitio web oficial del sitio principal", puedes controlar si la ShimoDocs entrada del sitio web oficial se muestra en las tarjetas de presentación personales.

Después de habilitarse, los usuarios pueden ver la entrada del sitio web oficial en su perfil personal.

### 2. Entrada "Acerca de"

A través de la opción "Habilitar entrada Acerca del sitio principal", puedes controlar si la entrada "Acerca de" se muestra en los perfiles personales.

Después de habilitarse, los usuarios pueden ver la entrada 'Acerca de' en su perfil personal.

### 3. Información de la marca

A través de la configuración "Si mostrar información de la marca", puedes controlar si la información de la marca se muestra a los usuarios en las páginas relevantes.

**Efecto de visualización:**

**Efecto oculto:**

## 6. Configurar marca de agua del sistema

### 1. Marca de agua del colaborador

Al configurar "Habilitar marca de agua de colaborador incorporada del sistema", puedes controlar el contenido de la marca de agua que se muestra cuando los usuarios editan o previsualizan archivos.

El contenido de la marca de agua variará dependiendo de si el visitante es anónimo y la elección de 'si mostrar información del usuario'. 

#### Acceso no anónimo 

| Opción de configuración | Contenido de visualización de la marca de agua |
| --- | --- |
| Mostrar/Ocultar | Mostrar la marca de agua incorporada del sistema, incluyendo información básica de autorización e información del usuario. |
| Personalizado | Mostrar según el contenido de la marca de agua del colaborador configurado en ShimoDocs Suite Empresa. |

#### Acceso anónimo

| Opción de configuración | Contenido de visualización de la marca de agua |
| --- | --- |
| Mostrar/Ocultar | Mostrar la marca de agua incorporada del sistema, incluyendo información básica de autorización e información del usuario. |
| Personalizado | Solo mostrar el texto personalizado configurado para usuarios anónimos. |

Después de habilitar la marca de agua de colaborador incorporada, la marca de agua correspondiente se mostrará permanentemente cuando los usuarios editen o previsualicen el archivo.

### 2. Marca de agua de la barra inferior del editor

A través de la configuración "Modificar marca de agua incorporada de la barra inferior del editor del sistema", puedes ajustar la marca de agua incorporada del sistema que se muestra en la parte inferior del editor.

Después de la configuración, puede ver el efecto real de la visualización en la parte inferior del editor.

## 7. Verificar Resultados de la Configuración

Después de completar la configuración, se recomienda verificar en el siguiente orden:

1. Confirme si el alcance de configuración seleccionado actualmente es global o de inquilino.
2. Abrir el ShimoDocs Suite página dentro del rango correspondiente.
3. Actualice la página y verifique el logo, el ícono del navegador, el nombre de la pestaña, el color del tema y el estado de visualización de la entrada.
4. Abra el archivo utilizando métodos anónimos y no anónimos, y confirme que el contenido de la marca de agua se muestra como se espera.
5. Si el efecto real no coincide con la expectativa, primero verifique si existe una configuración de inquilino con mayor prioridad.
