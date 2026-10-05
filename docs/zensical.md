# Zensical

## Instalación de Zensical

Primero crea un entorno virtual y actívalo, luego instala `zensical` con `pip`:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install zensical
```

Una vez instalado, para arrancar la documentación en el directorio desde el directorio donde se desea alojar, ejecuta el comando:

`zensical new .`

Esto crea la siguiente estructura:

```md
.
├─ .github/workflows
│  └─ docs.yml
├─ docs/
│  ├─ index.md
│  └─ markdown.md
└─ zensical.toml
```

Para utilizar zensical como contenido estático para un sitio web, debemos enlazarlo a `/var/www/nombre_sitio`, donde se ubicará `site/`.

Por ello, se debe establecer como root en el archivo de configuración en `etc/nginx/sites-available/static-site.conf`:

`root /var/www/static-site/site;`

```conf
server {
    listen 80;
    server_name www.static-site.com;
    root /var/www/static-site/site;
    index index.html;

    location / {
            try_files $uri $uri/ =404;
    }
}
```