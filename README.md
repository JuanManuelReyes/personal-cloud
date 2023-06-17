# Documentación del Proyecto: Servidor NextCloud

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
