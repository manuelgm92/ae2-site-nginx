# Navegación

A través del archivo `zensical.toml` se puede personalizar el sitio web. El archivo zensical.toml ya viene con una configuración por defecto y con diferentes opciones comentadas que no se están aplicando. Para aplicarlas bastaría con descomentarlas.

Una estructura de navegación clara y concisa es un aspecto importante de una buena documentación de proyectos. Zensical proporciona varias opciones para configurar el comportamiento de los elementos de navegación, incluidas pestañas y secciones, así como características como la navegación instantánea y las vistas previas instantáneas.

Se puede configurar una navegación adicional en el pie de página.

## Configuración

Por defecto, Zensical crea la barra lateral de navegación sobre la base de la estructura de carpetas y el contenido de las páginas de Markdown. Del mismo modo, utiliza un diseño predeterminado que se puede anular utilizando varias banderas de características descritas en esta página.

## Navegación explícita

Si desea ejercer más control sobre la estructura de su navegación, puede crear una definición explícita de la estructura de navegación en su archivo de configuración. En el caso más simple, simplemente enumera las rutas a sus archivos de contenido, dejando que Zensical extraiga un título para cada uno de ellos del propio contenido. Las rutas deben ser relativas al docs_dir.

```toml
[project]
nav = [
  "index.md",
  "about.md",
]
```

En lugar de dejar que Zensical descubra el título a utilizar para la entrada de navegación de una página, también puede especificar explícitamente un título:

```toml
[project]
nav = [
  { "Home" = "index.md" },
  { "About" = "about.md" },
]
```

### Secciones de navegación

Puede definir secciones de navegación para crear una jerarquía de navegación que guíe a sus usuarios a la información que necesitan.

```toml
[project]
nav = [
  { "Home" = "index.md" },
  { "About" = [
    "about/index.md",
    "about/vision.md",
    "about/team.md",
  ] },
]
```

### Enlaces externos

Los elementos de navegación suelen proporcionar una ruta a una página de rebajas. Sin embargo, cualquier cadena que no se pueda resolver en una página de Markdown se trata como una URL.

```toml
[project]
nav = [
  { "GitHub Repo" = "https://github.com/zensical/docs" },
]
```

La entrada de navegación "GitHub Repo" lleva al usuario al repositorio de la Documentación Zensical.

## Navegación instantánea

Cuando la navegación instantánea está habilitada, los clics en todos los enlaces internos serán interceptados y enviados a través de XHR sin volver a cargar completamente la página. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.instant",
]
```

La página resultante se analiza e inyecta e todos los controladores y componentes de eventos rebotan automáticamente, es decir, Zensical ahora se comporta como una aplicación de una sola página. Además, el índice de búsqueda se persiste a través de la navegación, lo que es especialmente útil para grandes sitios de documentación.

### Prefetching instantáneo

El prefetching instantáneo es una nueva función experimental que comenzará a buscar una página una vez que el usuario pase el cursor sobre un enlace. Esto reduce el tiempo de carga percibido para el usuario, especialmente en conexiones lentas, ya que la página estará disponible inmediatamente después de la navegación. Habilite con:

```toml
[project.theme]
features = [
  "navigation.instant",
  "navigation.instant.prefetch",
]
```

### Indicador de progreso

Con el fin de proporcionar una mejor experiencia de usuario en conexiones lentas cuando se utiliza la navegación instantánea, se puede habilitar un indicador de progreso. Se mostrará en la parte superior de la página y se ocultará una vez que la página se haya cargado por completo. Puedes habilitarlo en tu configuración con:

```toml
[project.theme]
features = [
  "navigation.instant",
  "navigation.instant.progress",
]
```

El indicador de progreso solo se mostrará si la página no ha terminado de cargarse después de 400 ms, por lo que las conexiones rápidas nunca lo mostrarán para una mejor experiencia instantánea.

## Vistas previas instantáneas

Las vistas previas instantáneas permiten al usuario previsualizar otro sitio de su documentación sin navegar hasta él. Pueden ser muy útiles para mantener al usuario en contexto. Las vistas previas instantáneas se pueden habilitar en cualquier enlace de encabezado con el atributo de `data-preview`:

``` markdown
[Attribute Lists](#){ data-preview }
```

### Vistas previas automáticas

La forma recomendada de trabajar con vistas previas instantáneas es usar la extensión Markdown que se incluye con Zensical, ya que le permite habilitar vistas previas instantáneas a nivel por página o por sección para su documentación:


```toml
[[project.markdown_extensions.zensical.extensions.preview.configurations]]
targets.include = [
  "customization.md",
  "compatibility/markdown/*",
]
```

La configuración anterior es la que utilizamos para nuestra documentación. Hemos habilitado las vistas previas instantáneas para nuestros registros de cambios, la guía de personalización y para todas las extensiones de Markdown en la guía de configuración.

## Seguimiento de anclaje

Cuando el seguimiento del ancla está habilitado, la URL en la barra de direcciones se actualiza automáticamente con el ancla activa como se destaca en la tabla de contenido. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.tracking",
]
```

## Pestañas de navegación

Cuando las pestañas están habilitadas, las secciones de nivel superior se representan en una capa de menú debajo del encabezado para ventanas de visualización por encima 1220px, pero permanecen como están en el móvil. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.tabs",
]
```

### Pestañas de navegación adhesivas

Cuando las pestañas adhesivas están habilitadas, las pestañas de navegación se bloquearán debajo del encabezado y siempre permanecerán visibles al desplazarse hacia abajo. Simplemente agregue las siguientes dos banderas de características a su configuración:

```toml
[project.theme]
features = [
  "navigation.tabs",
  "navigation.tabs.sticky",
]
```

## Secciones de navegación

Cuando las secciones están habilitadas, las secciones de nivel superior se representan como grupos en la barra lateral para las ventanas de visualización por encima de `1220px`, pero permanecen como están en el móvil. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.sections",
]
```

Ambas banderas de características, navigation.tabs y navigation.sections, pueden combinarse entre sí. Si ambas banderas de características están activadas, las secciones se renderizan para los elementos de navegación del nivel 2.

## Expansión de navegación

Cuando la expansión está habilitada, la barra lateral izquierda expandirá todas las subsecciones plegables por defecto, por lo que el usuario no tiene que abrir las subsecciones manualmente. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.expand",
]
```

## Ruta de navegación Breadcrumbs

Cuando se activan las rutas de navegación, se representa una navegación de miga de miga sobre el título de cada página, lo que podría facilitar la orientación para los usuarios que visitan su documentación en dispositivos con pantallas más pequeñas. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.path",
]
```

## Poda de navegación

Cuando se activa la poda, solo se incluyen los elementos de navegación visibles en el HTML renderizado, reduciendo el tamaño del sitio web construido en un 33 % o más. Añade las siguientes líneas a tu configuración:

```toml
[project.theme]
features = [
  "navigation.prune", 
]
```

Esta bandera de características es especialmente útil para sitios de documentación con miles de páginas, ya que la navegación constituye una fracción significativa del HTML. La poda de navegación reemplazará todas las secciones expandibles con enlaces a la primera página de esa sección (o a la página de índice de secciones).

## Páginas de índice de sección

Cuando las páginas de índice de sección están habilitadas, los documentos se pueden adjuntar directamente a las secciones, lo que es particularmente útil para proporcionar páginas de resumen. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.indexes", 
]
```

Para vincular una página a una sección, crea un nuevo documento con el nombre `index.md` en la carpeta correspondiente y añádelo al principio de tu sección de navegación:

```toml
[project]
nav = [
  { "Section" = [
    "section/index.md", 
    { "Page 1" = "section/page-1.md" },
    # ...
    { "Page n" = "section/page-n.md" },
  ] },
]
```

## Tabla de contenidos

### Anclador siguiendo

Cuando el seguimiento del anclaje para la tabla de contenido está habilitado, la barra lateral se desplaza automáticamente para que el ancla activa siempre esté visible. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "toc.follow",
]
```

### Integración de la navegación

Cuando la integración de navegación para la tabla de contenido está habilitada, siempre se representa como parte de la barra lateral de navegación a la izquierda. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "toc.integrate", 
]
```

## Botón de retroceso

Se puede mostrar un botón de retroceso hacia arriba cuando el usuario, después de desplazarse hacia abajo, comienza a desplazarse hacia arriba de nuevo. Se representa centrado y en la parte inferior de la página. Agregue las siguientes líneas a su configuración:

```toml
[project.theme]
features = [
  "navigation.top",
]
```

# Uso

## Ocultar las barras laterales

Las barras laterales de navegación y/o del índice pueden ocultarse para un documento con la propiedad `hide` el índice de portada. Añada las siguientes líneas en la parte superior de un archivo Markdown:

```hide
---
hide:
  - navigation
  - toc
---

# Page title
...
```

## Ocultar la ruta de navegación

Aunque la trayectoria de navegación se muestra por encima del titular principal, a veces puede ser deseable ocultarla para una página específica, lo cual se puede lograr con la propiedad `hide` el asunto principal:

```hide
---
hide:
  - path
---

# Page title
...
```

# Personalización

## Ancho del área de contenido

El ancho del área de contenido se establece para que la longitud de cada línea no supere los 80-100 caracteres, dependiendo del ancho de los caracteres. Si bien este es un valor predeterminado razonable, ya que las líneas más largas tienden a ser más difíciles de leer, puede ser deseable aumentar el ancho general del área de contenido, o incluso hacer que se extienda a todo el espacio disponible.

Esto se puede lograr fácilmente con una hoja de estilo adicional y unas pocas líneas de CSS:

```toml
[project]
extra_css = [
  "stylesheets/extra.css",
]
```
