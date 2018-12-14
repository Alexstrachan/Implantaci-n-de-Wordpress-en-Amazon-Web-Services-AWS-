# Practica10

# Implantación de Wordpress en Amazon Web Services (AWS)

Amazon Web Services es una colección de servicios de computación en la nube pública que en conjunto forman una plataforma de computación en la nube, ofrecidas a través de Internet por Amazon.com.

Para la realización de la practica utilizaremos el servicio de ``EC2`` de Amazon Web Service.

``Amazon EC2``. Amazon Elastic Compute Cloud (Amazon EC2) es un servicio web que proporciona capacidad informática en la nube segura y de tamaño modificable. Está diseñado para simplificar el uso de la informática en la nube a escala web para los desarrolladores.

## Guía de configuración e Instalación de una máquina con Wordpress con Amazon EC2.

1. Nos dirigiremos a ``Instances`` en el panel del margen izquierdo de EC2.
2. Pincharemos en ``Launch instance`` :
* 2.1. Debemos elegir una imagen para nuestra máquina virtual (``AMI``). En nuestro caso eligiremos la AMI: ``ubuntu-bionic-18.04-amd64-server-20180522-dotnetcore-2018.07.11 (ami-14fb1073)``.
* 2.2. Ahora elegiremos el tipo de instancia, utilizaremos la t2.micro.
* 2.3. En los detalles de la instancia, dejaremos la conifguración por defecto. 
* 2.4. Almacenamiento: También por defecto.
* 2.5. Etiquetas: Igual, la dejaremos por defecto.
* 2.6. Configuración de grupos de seguridad: En esta pestaña de configuración podemos configurar que protocolos tener activos y en que puertos. Por defecto vendrá abierto el TCP en el puerto 22. Añadiremos el protocolo HTTP y HTTPS con sus respectivos puertos 80 y 443.
* 2.7. Por último nos harán un resumen de nuestra conifguración e instalaremos la máquina que hemos elegido.

## Guía de instalación de Wordpress en la máquina Virtual.

#### Crear una Base de Datos y Usuario para Wordpress
1. Iniciar MySQL: `` mysql -u root -p``
2. crear una base de datos separada que WordPress pueda controlar: ``CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;``
3. Crear una cuenta: ``GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';``.
#### Instalar Extensiones Adicionales para PHP.
```
sudo apt-get update
sudo apt-get install php-curl php-gd php-mbstring php-mcrypt php-xml php-xmlrpc
```
```
sudo systemctl restart apache2
```
#### Ajustar la Configuración de Apache para Permitir Sobre-escritura y Re-escritura para .htaccess
crearemos ajustes menores para nuestra configuración de Apache. Actualmente, el uso de los archivos .htaccess está deshabilitado.
### Habilitar Sobre-escritura por .htaccess
Abrir el archivo de configuración primaria de Apache para hacer nuestro primer cambio:

Dentro de ``/etc/apache2/apache2.conf``:
```
/etc/apache2/apache2.conf
. . .

<Directory /var/www/html/>
    AllowOverride All
</Directory>

. . .
```
### Habilitar el Módulo de Re-escritura
``sudo a2enmod rewrite``
### Reiniciamos el servicio
``sudo systemctl restart apache2``
#### Descargar WordPress
Habrá que moverse a un directorio con permiso de escritura y posteriormente descargar la versión comprimida escribiendo:
```
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```
Extraemos el fichero: ``tar xzvf latest.tar.gz
``
Moveremos esos archivos a nuestro documento raíz en su momento. Antes de hacerlo, podemos agregar un archivo .htaccess de prueba y configurar sus permisos, de manera que esté disponible para ser utilizado por WordPress posteriormente.
```
touch /tmp/wordpress/.htaccess
chmod 660 /tmp/wordpress/.htaccess
```
Copiamos el archivo de configuración ejemplo a un archivo que WordPress pueda leer:
`` cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php``

Creamos el directorio upgrade: ``mkdir /tmp/wordpress/wp-content/upgrade``

Ahora podemos copiar todo el contenido del directorio en nuestro documento raíz: `` sudo cp -a /tmp/wordpress/. /var/www/html``


### Configurar el directorio de Wordpress.

Empezaremos asignando la propiedad de todos los archivos en nuestro documento raíz a nuestro usuario. Utilizaremos sammy como nuestro usuario en esta guía, pero tu deberías cambiar a uno que funcione con sudo en tu servidor. Asignaremos el grupo de propiedad al grupo www-data:
```
sudo chown -R sammy:www-data /var/www/html
```
Hay algunos permisos más que debemos ajustar. Primero, le daremos al grupo acceso de escritura a la carpeta wp-content para que la interfaz web pueda realizar cambios en nuestros temas y plugins:
```
sudo chmod g+w /var/www/html/wp-content
```
Ahora tenemos que darle al servidor web acceso  a nuestro contenido en estos directorios:
```
sudo chmod -R g+w /var/www/html/wp-content/themes
sudo chmod -R g+w /var/www/html/wp-content/plugins
```

### Configurar el archivo de Configuracion de Wordpress

```
nano /var/www/html/wp-config.php
```

Debe de verse algo asi:
```
. . .

define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

. . .
```
Y ahora configuraremos la base de datos:
Dentro de /var/www/html/wp-config.php
```
. . .

define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wordpressuser');

/** MySQL database password */
define('DB_PASSWORD', 'password');

. . .

define('FS_METHOD', 'direct');
```
### Instalación de Wordpress:

Nos iremos al navegador para acceder a la conifguración de wordpress: ``http://dominio_o_IP``.

Y a partir de aquí procederemos a su configuración por interfaz gráfica.


