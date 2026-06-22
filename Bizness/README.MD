### RESOLUCIÓN DE LA MÁQUINA BIZNESS

Una vez preparado nuestro laboratorio, vamos a hacer nuestro escaneo de puertos abiertos con nmap

Nos salen los puertos http y https y el ssh, ahora haremos un escaneo para ver qué servicios corren por esos puertos

```bash
sudo nmap -p80,443 -sCV -vvv -n 10.10.11.252
```

Vemos que el puerto 80 está abierto y sirve HTTP. Parece que hay un servidor web en ejecución con Nginx con la versión 1.18.0 este nos redirige a https en el que aparece un certificado pero al mirar las fechas parece que es un certificado autofirmado o mal configurado. Ahora nos dirigiremos a la web para echarle un vistazo.

Después de echar un vistazo a la web sin encontrar nada vamos a hacer una búsqueda de directorios en dirsearch
