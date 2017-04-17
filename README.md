#Ejercicio Apache  
  
Crear un servidor Apache con 4 virtual host:  
  
- www.gato.com -> mostrará la imagen de un gato.  
- www.mosquito.com -> mostrará la imagen de un mosquito tigre.  
- www.escherichiacoli.es -> mostrará la imagen de la bacteria escherichia coli. Sólo tendrá acceso el usuario 'user01'.  
- www.chip555.org -> mostrará la imagen del chip 555. Sólo tendrán acceso los usuarios del fichero de password creado.  
  
Crear una máquina con el servidor Apache y el servidor dns necesario. Crear otra máquina con un entorno gráfico para comprobar el correcto funcionamiento.  
  
Instalamos apache:  
  
~~~
apt-get install apache2
~~~

Configuracion Apache:  

Creamos los directorios donde irán las paginas web que a continuacion crearemos:  
  
~~~
sudo mkdir -p /var/www/gato.com/html  
sudo mkdir -p /var/www/mosquito.com/html  
sudo mkdir -p /var/www/escherichiacoli.es/html  
sudo mkdir -p /var/www/chip555.org/html  
~~~
  
Damos permisos:  
 
~~~ 
sudo chmod -R 777 /var/www
~~~
  
Creamos el sitio web en cada una de las paginas:  
  
~~~
echo "Gato" > /var/www/gato.com/html/index.html
echo "Mosquito" > /var/www/mosquito.com/html/index.html
echo "Bacteria escherichiacoli" > /var/www/escherichiacoli.es/html/index.html
echo "Chip 555" > /var/www/chip555.org/html/index.html
~~~
  
Copiamos la configuracion por defecto para nuestros sitios web:  
  
~~~
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/gato.com.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mosquito.com.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/escherichiacoli.es.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/chip555.org.conf
~~~
  
Configuramos los ficheros:
  
www.gato.com
~~~
<VirtualHost *:80>
    ServerAdmin admin@gato.com
    ServerName gato.com
    ServerAlias www.gato.com
    DocumentRoot /var/www/gato.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~
  
www.mosquito.com
~~~
<VirtualHost *:80>
    ServerAdmin admin@mosquito.com
    ServerName mosquito.com
    ServerAlias www.mosquito.com
    DocumentRoot /var/www/mosquito.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~
  
www.escherichiacoli.es
~~~
<VirtualHost *:80>
    ServerAdmin admin@escherichiacoli.es
    ServerName escherichiacoli.es
    ServerAlias www.escherichiacoli.es
    DocumentRoot /var/www/escherichiacoli.es/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~
  
www.chip555.org
~~~
<VirtualHost *:80>
    ServerAdmin admin@chip555.org
    ServerName chip555.org
    ServerAlias www.chip555.org
    DocumentRoot /var/www/chip555.org/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~

Habilitamos los sitios web con a2ensite de la siguiente forma:  
  
~~~
sudo a2ensite gato.com.conf
sudo a2ensite mosquito.com.conf
sudo a2ensite escherichiacoli.es.conf
sudo a2ensite chip555.org.conf
~~~
  
Deshabilitamos el sitio por defecto definido en 000-default.conf:  
  
~~~
sudo a2dissite 000-default.conf
~~~
  
Reiniciamos el demonio.
  
~~~
sudo service apache2 restart
~~~
  
Creamos el archivo de contraseñas:
  
~~~
sudo htpasswd -c /var/www/escherichiacoli.com/passwords user01
sudo htpasswd  /var/www/escherichiacoli.com/passwords user02
~~~
  
Añadiremos a nuestro archivo de configuracion lo siguiente:
  
~~~
user01:
<Directory /var/www/escherichiacoli.com/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/escherichiacoli.com/passwords
	Require user user01
</Directory>
<Directory /var/www/escherichiacoli.com/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from user01
</Directory>

user02:
<Directory /var/www/chip555.org/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/escherichiacoli.com/passwords
	Require valid-user
</Directory>
<Directory /var/www/chip555.org/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>
~~~
