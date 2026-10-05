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



