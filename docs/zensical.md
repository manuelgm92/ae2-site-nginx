# Zensical

## Índice

* [Instalación de Zensical](#instalación-de-zensical)
* [Creación y Estructura del Proyecto](#creación-y-estructura-del-proyecto)
* [Configuración de Nginx para Zensical](#configuración-de-nginx-para-zensical)
* [Compilación y Despliegue del Sitio Estático](#compilación-y-despliegue-del-sitio-estático)
* [Automatización y Flujo de Trabajo](#automatización-y-flujo-de-trabajo)

## Instalación de Zensical

Primero, crea un entorno virtual y actívalo para mantener las dependencias aisladas. Luego, instala `zensical` utilizando `pip`:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install zensical
```

Para verificar que la instalación se ha realizado correctamente, puedes comprobar la versión ejecutando:

```bash
zensical --version
```

## Creación y Estructura del Proyecto

Una vez instalado, sitúate en el directorio donde desees alojar tu proyecto y ejecuta el comando de inicialización:

`zensical new .`

Esto generará automáticamente la siguiente estructura de archivos y directorios:

```md
.
├─ .github/workflows
│  └─ docs.yml
├─ docs/
│  ├─ index.md
│  └─ markdown.md
└─ zensical.toml
```

* **zensical.toml**: Archivo de configuración principal del sitio.
* **docs/**: Directorio donde se redactará los archivos en formato Markdown.

## Configuración de Nginx para Zensical

A diferencia de un sitio web estático tradicional, Zensical genera una carpeta de compilación final llamada `site/` cuando procesa los archivos Markdown. Por lo tanto, para utilizar Zensical como contenido estático para tu servidor web, debes apuntar el parámetro root de Nginx directamente a esa subcarpeta de compilación (`/var/www/nombre_sitio/site`).

Edita o crea el archivo de configuración en `/etc/nginx/sites-available/nombre_archivo`, por ejemplo, en ``/etc/nginx/sites-available/static-site.conf`` con la siguiente estructura:

```conf
server {
    listen 80;
    server_name static-site.local www.static-site.local;
    root /var/www/static-site/site;
    index index.html;

    location / {
            try_files $uri $uri/ =404;
    }
}
```

## Compilación y Despliegue del Sitio Estático

Cada vez que realices modificaciones en los archivos Markdown dentro de la carpeta `docs/`, deberás compilar el proyecto para actualizar el sitio web estático.
Ejecuta el siguiente comando en la raíz de tu proyecto Zensical:

```bash
zensical build
```

Este comando procesará los archivos y actualizará el contenido del directorio `site/`. Gracias al enlace simbólico o la ruta establecida en Nginx, los cambios se verán reflejados de forma inmediata en tu servidor web.

## Automatización y Flujo de Trabajo

Para mantener tu sitio web actualizado de manera eficiente tras cada cambio en el contenido, el flujo de trabajo recomendado es el siguiente:

1. Modifica tus archivos `.md` en la carpeta `docs/`.
2. Compila el sitio ejecutando `zensical build`.
3. Si Nginx te devuelve algún error o los cambios no se reflejan, recuerda verificar la sintaxis de la configuración del servidor con `nginx -t` y recargarlo mediante `nginx -s reload`