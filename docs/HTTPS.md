## Paso 1 — Generar clave y certificado autofirmado

```bash
docker exec dpl-lab mkdir -p /etc/ssl/dpl
docker exec dpl-lab openssl req -x509 -nodes -newkey rsa:2048 -days 365 \
     -keyout /etc/ssl/dpl/www.alumno.local.key \
     -out /etc/ssl/dpl/www.alumno.local.crt \
     -subj "/CN=www.alumno.local"
docker exec dpl-lab chmod 400 /etc/ssl/dpl/www.alumno.local.key
```

**Por qué**: el certificado asocia tu dominio (CN) con una clave pública; el fichero .key es el secreto del servidor (ver HTTPS, certificados y SSL/TLS). -nodes deja la clave sin contraseña para que nginx la lea al arrancar.

**Resultado esperado**: dos ficheros en /etc/ssl/dpl/, con la clave en 400 (solo lectura para root).

## Paso 2 — Crear el server block HTTPS

```bash
docker exec dpl-lab bash -c 'cat > /etc/nginx/sites-available/003-www.alumno.local-ssl <<EOF
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name www.alumno.local;
    root /var/www/www.alumno.local;
    index index.html;

    ssl_certificate /etc/ssl/dpl/www.alumno.local.crt;
    ssl_certificate_key /etc/ssl/dpl/www.alumno.local.key;

    access_log /var/log/nginx/www-alumno-ssl.access.log;
    error_log /var/log/nginx/www-alumno-ssl.error.log;
}
EOF'
```

**Por qué**: HTTPS se configura por server block: el bloque con listen 443 ssl; y las rutas de clave/certificado (ver teoría). En nginx no hay que activar ningún módulo: ngx_http_ssl_module viene compilado.

**Resultado esperado**: el fichero existe en sites-available/.

## Paso 3 — Crear la redirección HTTP → HTTPS

Modifica el server block del puerto 80 de www.alumno.local para que solo redirija (edita 001-www.alumno.local):

```bash
server {
    listen 80;
    listen [::]:80;
    server_name www.alumno.local;
    return 301 https://$host$request_uri;
}
```

**Por qué**: return 301 responde con 301 Moved Permanently y la cabecera Location hacia la misma URL por HTTPS: el navegador (y curl -L) continúa solo. Así el sitio nunca sirve contenido por HTTP.

**Resultado esperado**: el fichero 001-www.alumno.local ya no tiene root ni access_log: solo la redirección.

## Paso 4 — Activar, validar y recargar

```bash
docker exec dpl-lab ln -s /etc/nginx/sites-available/003-www.alumno.local-ssl /etc/nginx/sites-enabled/
docker exec dpl-lab nginx -t          # syntax is ok
docker exec dpl-lab nginx -s reload
```
**Por qué**: el orden profesional: activar → validar sintaxis → aplicar. nginx -t evita dejar el servidor caído por un error de sintaxis.

**Resultado esperado**: syntax is ok y reload sin errores.

## Paso 5 — Comprobar con curl

Dentro del contenedor (no depende del puerto publicado):

```bash
docker exec dpl-lab curl -k -I https://localhost/ -H "Host: www.alumno.local"   # HTTP/1.1 200 OK
docker exec dpl-lab curl -I http://localhost/ -H "Host: www.alumno.local"       # 301 + Location: https://...
```

Desde el anfitrión (puerto 80 publicado; el 443 puede no estarlo):

```bash
curl -I -H "Host: www.alumno.local" http://localhost/   # HTTP/1.1 301 Moved Permanently
```
```md
Puerto 443 publicado

Si tu dpl-lab publica también el 443, comprueba desde el anfitrión con curl -k -I https://localhost/ -H "Host: www.alumno.local". Si no lo publica, usa siempre docker exec dpl-lab curl ... (el tráfico no sale del contenedor) y documéntalo en el README.
```

**Por qué**: -k (insecure) omite la verificación de la CA, necesaria en pruebas con autofirmados (ver teoría). Que curl sin -k falle con SSL certificate problem es correcto: significa que la verificación funciona.

**Resultado esperado**: el primer comando devuelve HTTP/1.1 200 OK; el del puerto 80, HTTP/1.1 301 con Location: https://www.alumno.local/.

## Paso 6 — Comprobar con openssl y con el navegador

```bash
docker exec dpl-lab openssl s_client -connect localhost:443 -servername www.alumno.local 2>&1 | grep -E "subject=|issuer=|Protocol|Cipher"
```

Abre después https://www.alumno.local/ en el navegador del anfitrión (si el 443 está publicado) o desde un contenedor con navegador: aparecerá el aviso de certificado no de confianza; en un entorno de pruebas se puede continuar (interiorízalo: esto no se hace jamás en producción).

**Por qué**: openssl s_client muestra el certificado que envía el servidor y el cifrado acordado: la verificación definitiva de que TLS se negocia.

**Resultado esperado**: subject=CN = www.alumno.local, issuer=CN = www.alumno.local (autofirmado), Protocol y Cipher con valores TLS actuales (orientativo: cambian según versión).

Verificación

```bash
docker exec dpl-lab nginx -t                                                   # syntax is ok
docker exec dpl-lab curl -k -I https://localhost/ -H "Host: www.alumno.local"  # 200 OK
curl -I -H "Host: www.alumno.local" http://localhost/                          # 301 (anfitrión)
docker exec dpl-lab openssl s_client -connect localhost:443 -servername www.alumno.local 2>&1 | head -20
docker exec dpl-lab tail -n 5 /var/log/nginx/www-alumno-ssl.access.log         # peticiones HTTPS
```

### **Resultado esperado**

* curl -k -I https://localhost/ (con Host) → HTTP/1.1 200 OK dentro del contenedor.
* curl -I http://localhost/ (con Host) → HTTP/1.1 301 con Location: https://... desde el anfitrión.
* openssl s_client conecta y muestra certificado válido (validez 365 días) y cifrado negociado.
* www-alumno-ssl.access.log registra las peticiones HTTPS con código 200 (log separado del :80).
* El navegador muestra tu página tras aceptar el aviso del certificado.


### Si algo falla

Problema|	Causa probable	|Comprobación	|Solución|
--|--|--|--|
nginx -t: cannot load certificate key	|Rutas de ssl_certificate/ssl_certificate_key mal escritas o sin permiso|	docker exec dpl-lab ls -l /etc/ssl/dpl/; nginx -t	|Corrige rutas del paso 1 y repite los permisos (clave 400)|
curl: (60) SSL certificate problem sin -k|	Es el comportamiento correcto con autofirmado	|—|	Usa -k en pruebas; certificado de CA en producción
bind() to 0.0.0.0:443 failed|	Otro servicio usa 443 dentro del contenedor	|docker exec dpl-lab ss -tlnp	|Libera el puerto o cambia de puerto|
Conexión rechazada en 443 desde el anfitrión	|El puerto 443 del contenedor no está publicado	|docker ps	|Comprueba dentro del contenedor (docker exec dpl-lab curl -k ...) o publica el 443 al recrear dpl-lab|
curl http://... sigue sirviendo contenido	|El server block :80 no se reemplazó por la redirección|	nginx -T	|Edita 001-www.alumno.local con el return 301 y recarga|
Clave con frase de paso	|La clave se generó protegida|	openssl rsa -in .key -check	|Regenera con -nodes (pruebas)|