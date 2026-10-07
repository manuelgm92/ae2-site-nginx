# Despliegue de sitio web con Jekyll y Nginx

## Índice

* [Requisitos previos](#requisitos-previos)
* [Despliegue en el servidor Nginx](#despliegue-en-el-servidor-nginx)

## Requisitos previos
* Instalar [Nginx](nginx.md)
* Instalar [Jekyll](jekyll.md)

## Despliegue en el servidor Nginx

Para el despliegue con el servidor nginx de un proyecto con las herramientas jekyll.

Hay que segir los siguientes pasos para poner a punto el servidor web:

*Nota: Se utilizará de ejemplo una web llamada `mgm-web.local`.*

1. Se crea el directorio en `/var/www/mgm-web`

2. Se crea el archivo `mgm-web.conf` en el directorio `/etc/nginx/sites-available/` con el siguiente contenido:

```conf
server {
	listen 80;
	server_name mgm-web.local www.mgm-web.local;
	root /var/www/mgm-web/_site;	
	index index.html index.htm;	
	
	location / {
		try_files $uri $uri/ =404; 		
	
	}
}
```

3. Se enlace el archivo a `sites-enabled`:

```bash
ln -s /etc/nginx/sites-available/mgm-web.conf /etc/nginx/sites-enabled/
```

4. Para tener comptabilidad con GitHub Pages manteniendo un sitio web activo y que sea compatible con este despliegue, se debe crear otro archivo _conf para nginx.

    * Se crea `_config-nginx.yml` con el siguiente contenido:

    ```yml
    url: "http://mgm-web.local"
    baseurl: ""
    ```

5. Se debe compilar el sitio combinando la configuración principal con la específica para Nginx (para asegurar que el baseurl quede vacío), desde el pc anfitrión ejecuta:

```bash
JEKYLL_ENV=production bundle exec jekyll build --config _config.yml,_config-nginx.yml
```

6. Copia el directorio `_site` del proyecto jekyll a `/var/www/mgm-web/`:

```bash
cp -r _site /var/www/mgm-web/
```

7. Aplicar los permisos de lectura y recargar Nginx:

```bash
sudo chown -R www-data:www-data /var/www/mgm-web
sudo chmod -R 755 /var/www/mgm-web
```

8. Ejecuta `nginx -s reload` para recargar el servidor nginx

9. Comprueba el funcionamiento del sitio.

*Nota: Recuerda introducir el server_name en /etc/hosts del equipo anfitrión y del contenedor docker. `127.0.0.1 mgm-web.local`*