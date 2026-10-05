# Nginx

## Índice


## Instalación de Nginx

Para instalar Nginx, ejecuta los siguientes comandos:

```bash
sudo apt update
sudo apt install nginx
```

Para visualizar la versión instalada, ejecuta el comando:

```bash
nginx -v
```

Ejemplo de salida: `nginx version: nginx/1.24.0 (Ubuntu)`

Para testear la configuración ejecuta el comando:

```bash
nginx -t
```

Si el archivo de configuración y el test es correcto, se obtiene la siguiente salida:

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## Configuración de inicio, parada y recarga
Para iniciar nginx, ejecute el archivo ejecutable. Una vez que nginx se inicia, se puede controlar invocando el ejecutable con el parámetro -s. Utilice la siguiente sintaxis:

`nginx -s signal`

Donde la señal puede ser una de las siguientes:

* stop — cierre rápido
* quit — apagado elegante
* reload — recargando el archivo de configuración
* reopen — reapertura de los archivos de registro

Por ejemplo, para detener los procesos nginx esperando que los procesos de trabajo terminen de servir las solicitudes actuales, se puede ejecutar el siguiente comando:

`nginx -s quit`

Los cambios realizados en el archivo de configuración no se aplicarán hasta que el comando para recargar la configuración se envíe a nginx o se reinicie. Para recargar la configuración, ejecute:

`nginx -s reload`

Una vez que el proceso maestro recibe la señal para recargar la configuración, comprueba la validez de la sintaxis del nuevo archivo de configuración e intenta aplicar la configuración proporcionada en él. Si esto es un éxito, el proceso maestro inicia nuevos procesos de trabajo y envía mensajes a los procesos de trabajo antiguos, solicitándoles que se cierren. De lo contrario, el proceso maestro retituye los cambios y continúa trabajando con la configuración anterior. Los procesos de trabajadores antiguos, que reciben una orden para cerrar, dejar de aceptar nuevas conexiones y continuar con el servicio de las solicitudes actuales hasta que se atiendan todas esas solicitudes. Después de eso, el antiguo trabajador procesa la salida.

Una señal también puede enviarse a los procesos de nginx con la ayuda de herramientas Unix como la utilidad `kill`. En este caso, una señal se envía directamente a un proceso con un ID de proceso dado. El ID de proceso del proceso maestro de nginx se escribe, por defecto, en `nginx.pid` en el directorio `/usr/local/nginx/logs` o `/var/run`. Por ejemplo, si el ID de proceso maestro es 1628, para enviar la señal QUIT que resulta en el cierre suave de nginx, ejecute:

`kill -s QUIT 1628`


Para obtener la lista de todos los procesos nginx que se están ejecutando, se puede utilizar la utilidad ps, por ejemplo, de la siguiente manera:

`ps -ax | grep nginx`

Para obtener más información sobre el envío de señales a nginx, consulte [Control de nginx](https://nginx.org/en/docs/control.html).

## Estructura del archivo de configuración

Nginx consiste en módulos que son controlados por directivas especificadas en el archivo de configuración. Las directivas se dividen en directivas simples y directivas de bloque. Una directiva simple consiste en el nombre y los parámetros separados por espacios y termina con un punto y coma (`;`). Una directiva de bloque tiene la misma estructura que una directiva simple, pero en lugar del punto y coma termina con un conjunto de instrucciones adicionales rodeadas de llaves (`{` y `}`). Si una directiva de bloque puede tener otras directivas dentro de llaves, se llama contexto (ejemplos: eventos, http, servidor y ubicación).

Las directivas colocadas en el archivo de configuración fuera de cualquier contexto se consideran que están en el contexto principal. Los `eventos` y las directivas `http` residen en el contexto `main`, el `server` en `http` y la `location` en `server`.

El resto de una línea después del signo `#` se considera un comentario.


## 1. ¿Dónde se configuran en NGINX?

Los archivos de configuración de tus sitios web en NGINX suelen encontrarse en la ruta:

`/etc/nginx/sites-available/`

Esta ruta actúa como una biblioteca o repositorio central de los sitios webs creados.

En ella, se encuentra por defecto el archivo `default` con la configuración por defecto de nginx. Para cada sitio web, se debe crear un archivo de configuración.

Un ejemplo de configuración para un sitio web estático podría ser el siguiente:

```conf
server {
    listen 80;
    server_name www.static-site.com;
    root /var/www/static-site;
    index index.html;

    location / {
            try_files $uri $uri/ =404;
    }
}
```

**Desglose de directivas**:

* `server` { ... } Define un nuevo bloque de servidor virtual (o virtual host). Permite configurar un sitio web independiente dentro del mismo servidor web NGINX.

* `listen 80;` Indica que este servidor escuchará las peticiones HTTP entrantes a través del puerto estándar 80 (el puerto predeterminado para tráfico web sin cifrar).

* `server_name www.static-site.com` Especifica el nombre de dominio (o subdominio) al que responderá este bloque de servidor. NGINX utiliza esta directiva para saber qué sitio web mostrar cuando recibe una petición con esa cabecera de host.

* root `/var/www/static-site;` Define la ruta raíz en el sistema de archivos del servidor Linux. Es el directorio físico donde NGINX buscará los archivos estáticos (HTML, CSS, imágenes, etc.) que forman parte del sitio web.

* `index index.html;` Establece el archivo predeterminado que se debe servir cuando un usuario accede a la raíz del sitio o a un directorio (por ejemplo, al entrar a [http://www.static-site.com/](http://www.static-site.com/), NGINX buscará automáticamente index.html).

* `location / { ... }` Bloque que define cómo procesar las peticiones URL que coincidan con la ruta raíz (`/`).

* `try_files $uri $uri/ =404;` Regla fundamental de enrutamiento que controla el orden de búsqueda ante una petición:
    1. Primero comprueba si el archivo exacto solicitado ($uri) existe en el disco.
    2. Si no existe, comprueba si corresponde a un directorio ($uri/).
    3. Si ninguna de las dos opciones anteriores existe, devuelve de forma limpia un error 404 Not Found.


Una vez creado el archivo de configuración del sitio web (en `sites-available`), Nginx todavía no lo está utilizando. Para que el servidor web lo reconozca y comience a servirlo, es necesario activarlo mediante un enlace simbólico que lo conecte a la carpeta de sitios activos (`sites-enabled`).


```bash
cd /etc/nginx/sites-enabled/
sudo ln -s ../sites-available/static-site.conf .
```

Una vez realizado estos pasos, testear la configuración para comprobar que todo está correcto: `nginx -t`

Gestión de Permisos y Propiedad para NGINX

Una vez colocados o enlazados los archivos estáticos en el directorio del servidor (por ejemplo, en `/var/www/static-site`), es fundamental asegurarse de que el servidor web NGINX tenga los permisos necesarios para leerlos. Si NGINX no puede acceder a estos archivos, devolverá errores de acceso (como un código 403 Forbidden o conflictos de lectura).

Para solucionar y prevenir esto, se ejecutan los siguientes comandos de administración:
1. `chown -R www-data:www-data /var/www/static-site`

www-data:www-data: Especifica el nuevo usuario (www-data) y el nuevo grupo (www-data). Este usuario es el que utiliza por defecto NGINX en sistemas basados en Ubuntu/Debian para ejecutar sus procesos de forma segura.
-R: Indica que el cambio debe aplicarse de forma recursiva, es decir, afectará a la carpeta principal, a todas sus subcarpetas y a todos los archivos que contenga en su interior.

2. `chmod -R 755 /var/www/static-site`

755: Define el esquema de permisos numérico:
El propietario (www-data) tiene permisos de lectura, escritura y ejecución (7). El grupo y otros usuarios externos tienen permisos de lectura y ejecución (5), lo que les permite ver y cargar los archivos web, pero no modificarlos. -R: Al igual que en el comando anterior, aplica la regla de forma recursiva a todo el árbol de directorios del sitio.
