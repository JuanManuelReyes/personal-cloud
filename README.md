# Documentación del Proyecto: Servidor NextCloud

Este proyecto tiene como objetivo ayudarte a instalar y configurar tu propio servidor NextCloud. NextCloud es una solución de almacenamiento en la nube de código abierto, que te permite mantener el control sobre tus propios datos.

## Requisitos previos Antes de comenzar, asegúrate de tener lo siguiente:
- Conocimientos básicos de administración de sistemas Linux, incluyendo cómo trabajar con la línea de comandos. 
- Una máquina con Ubuntu 20.04 (versión server o desktop).
- Acceso a internet para descargar los paquetes necesarios

## Software necesario:

-   Ubuntu 20.04 (versión server o desktop)
-   PHP 8.1 y módulos necesarios
-   Apache Web Server
-   MariaDB
-   NextCloud

## Pasos:

### Preparación del entorno:

1.  Verificar que la máquina virtual (en nuestro caso), tenga como adaptador de red el adaptador puente.
2.  Asegurarse de que la máquina tenga una IP fija, en nuestro caso 192.168.1.27.
3.  Verificar que nuestro sistema y repositorios estén actualizados (`sudo apt update` y `sudo apt upgrade`).

### Instalación de PHP 8.1:
```
    sudo apt install software-properties-common
    sudo add-apt-repository ppa:ondrej/php
    sudo apt update
    sudo apt upgrade
    sudo apt install php8.1
    sudo apt install php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-redis php8.1-intl -y
```
    
### Instalación y Configuración de MariaDB:

1.  Instalar MariaDB (`sudo apt install mariadb-server`).
    
2.  Configurar MariaDB (`mysql_secure_installation`).
    
    A. Cambiar o no la contraseña de root (en nuestro caso no).
    
    B. Remover todos los usuarios que sean anónimos.
    
    C. Deshabilitar la opción de conectarse como root remotamente.
    
    D. Eliminar la base de datos de prueba.
    
    E. Actualizar todos los privilegios de las tablas.
    
3.  Crear un usuario para Nextcloud:
```
    mysql -u root -p
    CREATE USER ‘nextcloud’@’localhost’ IDENTIFIED BY ‘nextcloud1234’;
```
4.  Crear la base de datos para Nextcloud:
```
    CREATE DATABASE nextcloud;
```
5.  Otorgar todos los privilegios al usuario Nextcloud:
```
    GRANT ALL PRIVILEGES ON nextcloud.* TO ‘nextcloud’@’localhost’;
    FLUSH PRIVILEGES;
 ```
6.  Salir de la base de datos con `exit`.

### Instalación y Configuración de Apache:

1.  Instalar Apache:
```
	sudo apt install -y apache2 libapache2-mod-php
```
2.  Crear el Virtual Host:
    
    A. Navegar a la carpeta de los sitios disponibles de Apache (`cd /etc/apache2/sites-available/`).
    
    B. Copiar la configuración del archivo default a un archivo para nuestro Nextcloud (`cp 000-default.conf nextcloud.conf`).
    
    C. Deshabilitar el sitio por defecto y habilitar nuestro sitio de Nextcloud (`a2dissite 000-default.conf && systemctl reload apache2`).
    
    D. Modificar el archivo `nextcloud.conf` (usar `nano nextcloud.conf`) y modificar la línea de `DocumentRoot` para que apunte a la carpeta de Nextcloud: `DocumentRoot /var/www/html/nextcloud`.
    
3.  Habilitar módulos necesarios:
```
	a2enmod rewrite dir mime env headers
```
4.  Crear la carpeta de Nextcloud (`mkdir /var/www/html/nextcloud`) y asignar los permisos adecuados al usuario `www-data`:

```
	chmod 750 /var/www/html/nextcloud/
	chown www-data:www-data /var/www/html/nextcloud/
```
5.  Descargar el archivo instalador de Nextcloud:
```
	cd /var/www/html/nextcloud 
	wget https://download.nextcloud.com/server/installer/setup-nextcloud.php 
	chown www-data:www-data setup-nextcloud.php
```
6.  Habilitar el sitio (`a2ensite nextcloud.conf && systemctl reload apache2`).

### Configuración del sitio web:

1.  Cada persona que dé de alta un servidor de Nextcloud puede configurarlo a su manera. En nuestro caso, vamos a hacer que el servidor tenga un certificado ssl (https) con un dominio personalizado.
    
2.  Conseguir un dominio DDNS de [freemyip.com](https://freemyip.com/).
    
3.  Comprobar la dirección con `curl` y el enlace que nos dio la página.
```
	curl https://freemyip.com/update?token=71c9d8bac78d46bda97c550c&domain=nextcloudobl2.freemyip.com
```
4.  Añadir un cronjob para mantener actualizada la IP:
```
crontab -e
* * * * * curl https://freemyip.com/update?token=71c9d8bac78d46bda97c550c&domain=nextcloudobl2.freemyip.com
```
5.  Configurar el router del servidor para permitir la redirección de puertos a los puertos 80 y 443.
    
6.  Conseguir un certificado de seguridad con [Certbot](https://certbot.eff.org/).
    
7.  Reconfigurar el archivo `config.php` de Nextcloud (`nano /var/www/html/nextcloud/config/config.php`).
    
8.  No es necesario modificar el archivo creado por Certbot porque heredó la configuración de nuestro `nextcloud.conf`.

## Solución de problemas 

Aquí podrías listar algunos problemas comunes que pueden surgir durante la instalación y cómo solucionarlos. Por ejemplo: 
- Problema: "El comando `sudo apt update` falla con un error de DNS." 
- Solución: Verifica que tu conexión a Internet esté funcionando y que tu archivo `/etc/resolv.conf` esté configurado correctamente. 
## Verificación final 

Una vez completada la instalación, puedes verificar que todo esté funcionando correctamente de la siguiente manera: 

- Accede a tu instalación de NextCloud a través de un navegador web. Deberías ver la interfaz de NextCloud y poder iniciar sesión con el usuario administrador que creaste durante la instalación. 
## Mantenimiento y soporte 

Es importante mantener tu servidor NextCloud actualizado para asegurarte de que estás protegido contra cualquier vulnerabilidad de seguridad que pueda haber sido descubierta. Puedes hacer esto ejecutando `sudo apt update` y `sudo apt upgrade` regularmente. 

Si encuentras problemas, hay varios lugares donde puedes buscar ayuda:

- La [documentación oficial de NextCloud](https://docs.nextcloud.com/)
- Los [foros de NextCloud](https://help.nextcloud.com/)

## Seguridad 

Recuerda que es importante mantener tu servidor NextCloud seguro. Algunos pasos que puedes seguir incluyen:
- Configurar un firewall para proteger tu servidor. 
- Asegurarte de que todas las comunicaciones con tu servidor estén cifradas utilizando SSL. 
- Implementar políticas de contraseñas fuertes para los usuarios de tu servidor NextCloud. 
- Considerar la posibilidad de utilizar autenticación de dos factores para añadir una capa adicional de seguridad.
