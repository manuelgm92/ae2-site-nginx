## Paso 1 — Crear los directorios de contenido

```bash
docker exec dpl-lab mkdir -p /var/www/www.alumno.local /var/www/app.alumno.local
```

**Por qué**: cada sitio tiene su propio root: así un solo nginx sirve contenidos independientes.

**Resultado esperado**: docker exec dpl-lab ls -ld /var/www/www.alumno.local /var/www/app.alumno.local muestra dos directorios con propietario root.

## Paso 2 — Crear los índices de cada sitio

```bash
docker exec dpl-lab bash -c 'cat > /var/www/www.alumno.local/index.html <<EOF
<!DOCTYPE html>
<html lang="es">
<head><meta charset="UTF-8"><title>www.alumno.local</title></head>
<body>
<h1>Bienvenida a la Web publica de ejemplo</h1>
<p>Servida por el server block www.alumno.local</p>
</body>
</html>
EOF'
```

Repite con app.alumno.local/index.html y un título "Aplicación" y el texto "Acceso a la aplicacion interna". Guarda una copia de ambos en tu microproyecto.

**Por qué**: los contenidos distintos demuestran que cada server block sirve su propio root.

**Resultado esperado**: docker exec dpl-lab diff /var/www/www.alumno.local/index.html /var/www/app.alumno.local/index.html devuelve diferencias (los ficheros son distintos).

## Paso 3 — Crear los ficheros de configuración de los sitios

```bash
docker exec dpl-lab bash -c 'cat > /etc/nginx/sites-available/001-www.alumno.local <<EOF
server {
    listen 80;
    listen [::]:80;
    server_name www.alumno.local;
    root /var/www/www.alumno.local;
    index index.html;

    access_log /var/log/nginx/www-alumno.access.log;
    error_log /var/log/nginx/www-alumno.error.log;
}
EOF'

docker exec dpl-lab bash -c 'cat > /etc/nginx/sites-available/002-app.alumno.local <<EOF
server {
    listen 80;
    listen [::]:80;
    server_name app.alumno.local;
    root /var/www/app.alumno.local;
    index index.html;

    access_log /var/log/nginx/app-alumno.access.log;
    error_log /var/log/nginx/app-alumno.error.log;
}
EOF'
```

**Por qué**: cada fichero es un sitio; server_name es la clave del enrutamiento por nombre, y los logs separados permiten auditar cada dominio (ver Server blocks).

**Resultado esperado**: docker exec dpl-lab ls /etc/nginx/sites-available/ lista los dos ficheros nuevos (junto a default).

## Paso 4 — Activar los sitios y comprobar la sintaxis

```bash
docker exec dpl-lab ln -s /etc/nginx/sites-available/001-www.alumno.local /etc/nginx/sites-enabled/
docker exec dpl-lab ln -s /etc/nginx/sites-available/002-app.alumno.local /etc/nginx/sites-enabled/
docker exec dpl-lab rm /etc/nginx/sites-enabled/default    # desactivar el sitio por defecto
docker exec dpl-lab nginx -t                               # syntax is ok
docker exec dpl-lab nginx -s reload
```

**Por qué**: en nginx los sitios se activan con enlaces simbólicos en sites-enabled/ (no hay a2ensite); nginx -t valida la sintaxis antes de aplicar y nginx -s reload la aplica sin cortar el servicio.

**Resultado esperado**: nginx -t imprime syntax is ok y el reload no muestra errores. docker exec dpl-lab ls /etc/nginx/sites-enabled/ incluye los dos enlaces y ya no el default.

## Paso 5 — Comprobar con curl usando la cabecera Host

Desde el anfitrión (el puerto 80 del contenedor está publicado):

```bash
curl -I -H "Host: www.alumno.local" http://localhost/
curl -I -H "Host: app.alumno.local" http://localhost/
curl -H "Host: www.alumno.local" http://localhost/    # contenido de la Web pública
curl -H "Host: app.alumno.local" http://localhost/    # contenido de la aplicación
```

**Por qué**: -H "Host: ..." simula que el cliente pide ese sitio sin depender de DNS (ver HTTP y HTTPS).

**Resultado esperado**: la primera devuelve HTTP/1.1 200 OK y el HTML de la Web; la segunda, idem para la aplicación, con contenidos distintos.

### Verificación

```bash
# En el anfitrión
curl -I -H "Host: www.alumno.local" http://localhost/   # 200 OK
curl -I -H "Host: app.alumno.local" http://localhost/   # 200 OK
# En el contenedor: sitios cargados y logs separados
docker exec dpl-lab nginx -T | grep -E "server_name|root"
docker exec dpl-lab ls /var/log/nginx/ | grep alumno
```

### **Resultado esperado**

* Ambos curl -I -H "Host: ..." devuelven 200 OK con contenidos distintos.
* nginx -T muestra los dos server blocks con sus server_name y root.
* Tras visitar ambos, cada log contiene solo sus propias peticiones:

```bash
docker exec dpl-lab tail -n 3 /var/log/nginx/www-alumno.access.log
docker exec dpl-lab tail -n 3 /var/log/nginx/app-alumno.access.log
```

### Si algo falla

Problema |	Causa probable |	Comprobación |	Solución |
--|--|--|--|
nginx -t da error|	Sintaxis en el fichero del sitio|	El mensaje indica fichero y línea	|Corrige y repite el paso 4|
Las dos URLs sirven el mismo contenido	|Los sitios no están activos o server_name duplicado	|ls sites-enabled/; nginx -T|	Crea los enlaces de nuevo + nginx -t + reload|
curl -H "Host: ..." devuelve el contenido del default|	El sitio por defecto sigue activo y es el primer server block	|ls /etc/nginx/sites-enabled/	|rm del enlace default y reload|
404 en uno de los sitios	|root vacío o índice con otro nombre	|docker exec dpl-lab ls /var/www/www.alumno.local/|	Crea index.html en el root correcto|
Cambios no aplican	|Olvidaste recargar	|nginx -s reload	|Recarga y repite la verificación|
El navegador no encuentra www.alumno.local	|Sin DNS y sin hosts|	ping www.alumno.local	|Usa curl -H "Host: ..." o añade la línea en /etc/hosts|