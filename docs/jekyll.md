# Jekyll

## Índice

* [Instalación de Jekyll](#instalación-de-jekyll)
* [Jekyll Theme](#jekyll-theme)
* [Configuración del sitio](#configuración-del-sitio)
* [_data/navigation.yml](#_datanavigationyml)
* [_pages](#_pages)
* [assets](#assets)
* [_posts](#_posts)
* [ollections (Portafolio y proyectos)](##collections-portafolio-y-proyectos)

## Instalación de jekyll

**1. Instala todos los requisitos previos en función del sistema opertativo: [Requisitos previos](https://jekyllrb.com/docs/installation/)**

**2. Instala las gemas jekyll y bundler:**

 `gem install jekyll bundler`

Los Gems son código que puedes incluir en proyectos de Ruby. 

`Gemfile` es una lista de gemas utilizadas por su sitio. Cada sitio Jekyll tiene un Gemfile en la carpeta principal.

Bundler es una gema que instala todas las gemas en tu Gemfile.

Para instalar gemas en tu Gemfile usando Bundler, ejecuta lo siguiente en el directorio que tiene el Gemfile:

```bash
bundle install
bundle exec jekyll serve
```

**3. Crea un nuevo sitio Jekyll en `./myblog`.**
```bash
jekyll new myblog
```

Para ejecutar el sitio en local, dentro del directorio:

```bash
cd myblog
bundle exec jekyll serve
```

Navega a http://localhost:4000

## Jekyll Theme

Se utilizará el siguiente theme de este repositorio [GitHub](https://github.com/mmistakes/minimal-mistakes)

Hay tres formas de instalar: como un tema basado en gemas, como un tema remoto (compatible con GitHub Pages) o biforcar/copiar directamente todos los archivos del tema en su proyecto. Para nuestro caso se aplicará el método remoto.

Con temas basados en Gem, los directorios como los `assets`, `_layouts`, `_includes` y `_sass` se almacenan en el gem del tema, ocultos de tu vista inmediata. Sin embargo, todos los directorios necesarios se leerán y procesarán durante el proceso de construcción de Jekyll.

Esto permite una instalación y actualización más fáciles, ya que no tiene que administrar ninguno de los archivos del tema. Para instalar:

1. Añade lo siguiente a tu Gemfile:

```bash
gem "minimal-mistakes-jekyll"
```

2. Obtenga y actualice las gemas agrupadas ejecutando el siguiente comando Bundler:

```bash
bundle
```

3. Configura el tema en el archivo Jekyll _config.yml de tu proyecto:

```yml
theme: minimal-mistakes-jekyll
```

Para actualizar el tema, ejecute `bundle update`.

### Método de tema remoto

Los temas remotos son similares a los temas basados en Gem, pero no requieren cambios en Gemfile ni listado blanco, lo que los hace ideales para sitios alojados con GitHub Pages.

Para instalar:

1. Crea/reemplaza el contenido de tu Gemfile con lo siguiente:

```bash
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
gem "jekyll-include-cache", group: :jekyll_plugins
```

2. Añade `jekyll-include-cache` a los plugins de `_config.yml`.

3. Obtenga y actualice las gemas agrupadas ejecutando el siguiente comando Bundler:

```bash
bundle
```

4. Añade `remote_theme: "mmistakes/minimal-mistakes@4.28.1"` a tu archivo `_config.yml`. Elimina cualquier otro `theme:` o entrada `remote_theme`.

### Fork/Clonar el repositorio

Este método tendría todos los logs del repositorio.

En nuestro caso, para poner en marcha de forma rápida el proyecto, evitar errores de dependencias y versiones, y tener una plantilla sobre la que trabajar, nos descargaremos el `.zip` del repositorio en local. 

Para instalar:

1. Descargar `.zip`del repositorio [Github](https://github.com/mmistakes/minimal-mistakes)

2. Copiar todos los archivos y directorios en el directorio deseado y ejecutar `git init`...

3. Ejecutar `bundle install``

4. Probar ejecución con `bundle exec jekyll serve``

## Configuración del sitio

### _config.yml

El archivo `_config.yml` es el cerebro y el centro de control principal de cualquier sitio web creado con Jekyll (y por tanto, de Minimal Mistakes). Es un archivo de configuración escrito en formato YAML que le dice a Jekyll cómo debe procesar, compilar y mostrar tu página web.
Prácticamente todo lo que define la identidad, el comportamiento global y el diseño general de tu sitio se centraliza ahí.

Al tener una "plantilla" de `_config.yml` podemos comentar y descomentar muchas opciones editandolas a nuestro gusto.

```yml
# Welcome to Jekyll!
#
# This config file is meant for settings that affect your entire site, values
# which you are expected to set up once and rarely need to edit after that.
# For technical reasons, this file is *NOT* reloaded automatically when you use
# `jekyll serve`. If you change this file, please restart the server process.

# Theme Settings
#
# Review documentation to determine if you should use `theme` or `remote_theme`
# https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#installing-the-theme

# theme                  : "minimal-mistakes-jekyll"
# remote_theme           : "mmistakes/minimal-mistakes"
minimal_mistakes_skin    : "default" # "air", "aqua", "contrast", "dark", "dirt", "neon", "mint", "plum", "sunrise", "catppuccin_latte", "catppuccin_mocha"

# Site Settings
locale                   : "en-US"
direction                : # "ltr" (default), "rtl" # set the direction of the page
title                    : "Site Title"
title_separator          : "-"
subtitle                 : # site tagline that appears below site title in masthead
name                     : "Your Name"
description              : "An amazing website."
url                      : # the base hostname & protocol for your site e.g. "https://mmistakes.github.io"
baseurl                  : # the subpath of your site, e.g. "/blog"
repository               : # GitHub username/repo-name e.g. "mmistakes/minimal-mistakes"
teaser                   : # path of fallback teaser image, e.g. "/assets/images/500x300.png"
logo                     : # path of logo image to display in the masthead, e.g. "/assets/images/88x88.png"
masthead_title           : # overrides the website title displayed in the masthead, use " " for no title
breadcrumbs              : # true, false (default)
words_per_minute         : 200
enable_copy_code_button  : # true, false (default)
copyright                : # "copyright" name, defaults to site.title
copyright_url            : # "copyright" URL, defaults to site.url
comments:
  provider               : # false (default), "disqus", "discourse", "facebook", "staticman", "staticman_v2", "utterances", "giscus", "custom"
  disqus:
    shortname            : # https://help.disqus.com/customer/portal/articles/466208-what-s-a-shortname-
  discourse:
    server               : # https://meta.discourse.org/t/embedding-discourse-comments-via-javascript/31963 , e.g.: meta.discourse.org
  facebook:
    # https://developers.facebook.com/docs/plugins/comments
    appid                :
    num_posts            : # 5 (default)
    colorscheme          : # "light" (default), "dark"
  utterances:
    theme                : # "github-light" (default), "github-dark"
    issue_term           : # "pathname" (default)
  giscus:
    repo_id              : # Shown during giscus setup at https://giscus.app
    category_name        : # Full text name of the category
    category_id          : # Shown during giscus setup at https://giscus.app
    discussion_term      : # "pathname" (default), "url", "title", "og:title"
    reactions_enabled    : # '1' for enabled (default), '0' for disabled
    theme                : # "light" (default), "dark", "dark_dimmed", "transparent_dark", "preferred_color_scheme"
    strict               : # 1 for enabled, 0 for disabled (default)
    input_position       : # "top", "bottom" # The comment input box will be placed above or below the comments
    emit_metadata        : # 1 for enabled, 0 for disabled (default) # https://github.com/giscus/giscus/blob/main/ADVANCED-USAGE.md#imetadatamessage
    lang                 : # "en" (default)
    lazy                 : # true, false # Loading of the comments will be deferred until the user scrolls near the comments container.
  staticman:
    branch               : # "master"
    endpoint             : # "https://{your Staticman v3 API}/v3/entry/github/"
reCaptcha:
  siteKey                :
  secret                 :
atom_feed:
  path                   : # blank (default) uses feed.xml
  hide                   : # true, false (default)
search                   : # true, false (default)
search_full_content      : # true, false (default)
search_provider          : # lunr (default), algolia, google
lunr:
  search_within_pages    : # true, false (default)
algolia:
  application_id         : # YOUR_APPLICATION_ID
  index_name             : # YOUR_INDEX_NAME
  search_only_api_key    : # YOUR_SEARCH_ONLY_API_KEY
  powered_by             : # true (default), false
google:
  search_engine_id       : # YOUR_SEARCH_ENGINE_ID
  instant_search         : # false (default), true
# SEO Related
google_site_verification :
bing_site_verification   :
naver_site_verification  :
yandex_site_verification :
baidu_site_verification  :

# Social Sharing
twitter:
  username               :
facebook:
  username               :
  app_id                 :
  publisher              :
og_image                 : # Open Graph/Twitter default site image
og_image_alt             : # Alt text for Open Graph/Twitter default site image
# For specifying social profiles
# - https://developers.google.com/structured-data/customize/social-profiles
social:
  type                   : # Person or Organization (defaults to Person)
  name                   : # If the user or organization name differs from the site's name
  links: # An array of links to social media profiles

# Analytics
analytics:
  provider               : # false (default), "google", "google-universal", "google-gtag", "custom"
  google:
    tracking_id          :
    anonymize_ip         : # true, false (default)


# Site Author
author:
  name             : "Your Name"
  avatar           : # path of avatar image, e.g. "/assets/images/bio-photo.jpg"
  bio              : "I am an **amazing** person."
  location         : "Somewhere"
  email            :
  # fediverse      : "@you@instance.social"  # used for fediverse:creator meta tag
  links:
    - label: "Email"
      icon: "fas fa-fw fa-square-envelope"
      # url: "mailto:your.name@email.com"
    - label: "Website"
      icon: "fas fa-fw fa-link"
      # url: "https://your-website.com"
    - label: "Twitter"
      icon: "fab fa-fw fa-square-x-twitter"
      # url: "https://twitter.com/"
    - label: "Facebook"
      icon: "fab fa-fw fa-square-facebook"
      # url: "https://facebook.com/"
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      # url: "https://github.com/"
    - label: "Instagram"
      icon: "fab fa-fw fa-instagram"
      # url: "https://instagram.com/"

# Site Footer
footer:
  links:
    - label: "Twitter"
      icon: "fab fa-fw fa-square-x-twitter"
      # url:
      # rel: "me"  # optional: adds rel attribute (e.g. for IndieWeb web sign-in)
    - label: "Facebook"
      icon: "fab fa-fw fa-square-facebook"
      # url:
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      # url:
    - label: "GitLab"
      icon: "fab fa-fw fa-gitlab"
      # url:
    - label: "Bitbucket"
      icon: "fab fa-fw fa-bitbucket"
      # url:
    - label: "Instagram"
      icon: "fab fa-fw fa-instagram"
      # url:
  since: "2013"


# Reading Files
include:
  - .htaccess
  - _pages
exclude:
  - "*.sublime-project"
  - "*.sublime-workspace"
  - vendor
  - .asset-cache
  - .bundle
  - .jekyll-assets-cache
  - .sass-cache
  - assets/js/plugins
  - assets/js/_main.js
  - assets/js/vendor
  - Capfile
  - CHANGELOG
  - config
  - Gemfile
  - Gruntfile.js
  - gulpfile.js
  - LICENSE
  - log
  - minimal-mistakes-jekyll.gemspec
  - node_modules
  - package.json
  - package-lock.json
  - Rakefile
  - README
  - tmp
  - /docs # ignore Minimal Mistakes /docs
  - /test # ignore Minimal Mistakes /test
keep_files:
  - .git
  - .svn
encoding: "utf-8"
markdown_ext: "markdown,mkdown,mkdn,mkd,md"


# Conversion
markdown: kramdown
highlighter: rouge
lsi: false
excerpt_separator: "\n\n"
incremental: false


# Markdown Processing
kramdown:
  input: GFM
  hard_wrap: false
  auto_ids: true
  footnote_nr: 1
  entity_output: as_char
  toc_levels: 1..6
  smart_quotes: lsquo,rsquo,ldquo,rdquo
  enable_coderay: false


# Sass/SCSS
sass:
  sass_dir: _sass
  style: compressed # https://sass-lang.com/documentation/file.SASS_REFERENCE.html#output_style
  quiet_deps: true
  silence_deprecations: ['import']


# Outputting
permalink: /:categories/:title/
timezone: # https://en.wikipedia.org/wiki/List_of_tz_database_time_zones


# Pagination with jekyll-paginate
paginate: 5 # amount of posts to show
paginate_path: /page:num/

# Pagination with jekyll-paginate-v2
# See https://github.com/sverrirs/jekyll-paginate-v2/blob/master/README-GENERATOR.md#site-configuration
#   for configuration details
pagination:
  # Set enabled to true to use paginate v2
  # enabled: true
  debug: false
  collection: 'posts'
  per_page: 10
  permalink: '/page/:num/'
  title: ':title - page :num'
  limit: 0
  sort_field: 'date'
  sort_reverse: true
  category: 'posts'
  tag: ''
  locale: ''
  trail:
    before: 2
    after: 2


# Plugins (previously gems:)
plugins:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-gist
  - jekyll-feed
  - jekyll-include-cache

# mimic GitHub Pages with --safe
whitelist:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-gist
  - jekyll-feed
  - jekyll-include-cache


# Archives
#  Type
#  - GitHub Pages compatible archive pages built with Liquid ~> type: liquid (default)
#  - Jekyll Archives plugin archive pages ~> type: jekyll-archives
#  Path (examples)
#  - Archive page should exist at path when using Liquid method or you can
#    expect broken links (especially with breadcrumbs enabled)
#  - <base_path>/tags/my-awesome-tag/index.html ~> path: /tags/
#  - <base_path>/categories/my-awesome-category/index.html ~> path: /categories/
#  - <base_path>/my-awesome-category/index.html ~> path: /
category_archive:
  type: liquid
  path: /categories/
tag_archive:
  type: liquid
  path: /tags/
# show_taxonomy: false  # set to false to hide tag/category lists on posts
# https://github.com/jekyll/jekyll-archives
# jekyll-archives:
#   enabled:
#     - categories
#     - tags
#   layouts:
#     category: archive-taxonomy
#     tag: archive-taxonomy
#   permalinks:
#     category: /categories/:name/
#     tag: /tags/:name/


# HTML Compression
# - https://jch.penibelst.de/
compress_html:
  clippings: all
  ignore:
    envs: development


# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: # true
      share: true
      related: true
```

### _data/navigation.yml

El archivo `_data/navigation.yml` funciona como el panel de control central para definir y estructurar las opciones que aparecen en el menú de navegación (la barra superior o lateral) de tu sitio web en Jekyll.

```yml
# main links
main:
  - title: "CV"
    url: "/cv/"
  #- title: "Minimal Mistakes Guide"
  #  url: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
```


### _pages

Para crear nuevas páginas, se debe crear el directorio `_pages`, donde se añadirá el contenido en formato `.md`o `.html`

En cada archivo creado, se debe incluir el Font Matter (o metadatos). Es un bloque de código escrito en formato YAML que se coloca siempre al inicio de los archivos Markdown en generadores de sitios estáticos como Jekyll.

Este bloque no se muestra directamente como texto en la página web, sino que le da instrucciones a Jekyll sobre cómo debe procesar y mostrar ese archivo específico.

* `layout: single`: Le indica a Jekyll qué plantilla de diseño utilizar para esta página. El diseño single (muy común en Minimal Mistakes) muestra una página de contenido individual con la estructura estándar del sitio (barra lateral, espacio de lectura limpio, etc.).
* `title: "Mi Currículum Vitae"`: Define el título principal que aparecerá en la pestaña del navegador (la etiqueta <title>) y, dependiendo del diseño, como encabezado visible de la página.
* `permalink: /cv/`: Establece la URL amigable o dirección web personalizada donde se podrá acceder a esta página. En lugar de llamarse cv.html o cv.html, tu web lo publicará de forma limpia en tuusuario.github.io/tu-repositorio/cv/.
* `author_profile: true`: Activa o desactiva la barra lateral con tu foto de perfil y tus datos personales (la tarjeta de presentación del autor). Al ponerlo en true, le dices que mantenga esa barra visible a la izquierda mientras los usuarios leen tu currículum.

```md
---
layout: single
title: "Mi Currículum Vitae"
permalink: /cv/
author_profile: true
---
```

### assets

En el directorio `assets/` es donde se almacenan todos los elementos multimedia, estilos visuales y scripts interactivos que dan vida y diseño a tu sitio web en Jekyll.

```md
assets/
├── CV.pdf
├── css
│   └── main.scss
├── images
│   └── foto_profile.jpg
└── js
    ├── _main.js
    ├── lunr
    │   ├── lunr-en.js
    │   ├── lunr-gr.js
    │   ├── lunr-store.js
    │   ├── lunr.js
    │   └── lunr.min.js
    ├── main.min.js
    ├── main.min.js.map
    ├── plugins
    │   ├── gumshoe.js
    │   ├── jquery.ba-throttle-debounce.js
    │   ├── jquery.fitvids.js
    │   ├── jquery.greedy-navigation.js
    │   ├── jquery.magnific-popup.js
    │   └── smooth-scroll.js
    └── vendor
        └── jquery
            └── jquery-3.6.0.js
```

### _posts

Para crear entradas de blog o artículos, se utiliza el directorio `_posts`. Los archivos deben tener obligatoriamente un formato de nombre específico basado en la fecha: `YYYY-MM-DD-título.md`.

Ejemplo: `2026-06-07-mi-primer-post.md`

Dentro de cada post se incluye su propio Front Matter:

```md
---
layout: single
title: "Mi primer artículo en el blog"
date: 2026-06-07 12:00:00 +0100
categories: [desarrollo, jekyll]
tags: [git, web]
---

Contenido del artículo en Markdown...
```

Para más información sobre la configuración del sitio basado en Jekyll con el theme **minimal-mistakes** pulse [Aquí](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/).

### Collections (Portafolio y proyectos)

Las colecciones en Jekyll permiten agrupar contenido personalizado que no son ni entradas de blog (`_posts`) ni páginas estáticas (`_pages`). En el tema Minimal Mistakes, se utilizan principalmente para crear secciones como un portafolio.

1. Configuración en `_config.yml`
Para habilitar una nueva colección (por ejemplo, `portfolio`), añade lo siguiente al archivo de configuración:

```yml
collections:
  portfolio:
    output: true
    permalink: /:collection/:path/
```

2. Crea un archivo dentro de tu directorio `_pages/` (por ejemplo, `portfolio.md`) para mostrar la cuadrícula de proyectos:

```md
---
layout: collection
title: "Mi Portafolio"
permalink: /portfolio/
collection: portfolio
entries_layout: grid
classes: wide
---

Descripción general del portafolio.
```

3. Añadir elementos a la colección

Crea una carpeta en la raíz del proyecto llamada con un guion bajo y el nombre de la colección (`_portfolio/`). Dentro, cada archivo `.md` representará un proyecto individual (ej. `spring-hotel-app.md`), utilizando Front Matter para configurar su portada, miniaturas y diseño:

```md
---
title: "Nombre del Proyecto"
excerpt: "Breve descripción..."
author_profile: false
classes: wide
header:
  image: "url_imagen_banner.jpg"
  teaser: "url_imagen_miniatura.jpg"
---

Contenido detallado del proyecto en Markdown...
```