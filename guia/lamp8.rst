instalar LAMP Stack en AlmaLinux 8.4
===========================

Instalar Apache en AlmaLinux
-----------------------------

Comenzamos con la instalación del servidor web Apache. El paquete Apache httpd está alojado en el repositorio de AppStream. Como tal, puede instalar Apache usando el administrador de paquetes DNF de la siguiente manera::

	dnf install -y @httpd	

	systemctl start httpd

	systemctl enable httpd

	systemctl status httpd

Otorgamos permisos en el firewall::

	firewall-cmd --permanent --zone=public --add-service=http

	firewall-cmd --permanent --zone=public --add-service=https

Verificamos ::

	http://server-ip-address
	OR
	http://your-domain.com

Instalar MariaDB en AlmaLinux
---------------

Instalar MariaDB: es un sistema de administración de bases de datos relacionales (RDBMS) que es una bifurcación (fork) de MySQL. MariaDB también está disponible en el repositorio de AppStream. Puede enumerar las versiones disponibles de MariaDB ejecutando el siguiente comando::


	dnf module list mariadb

::

	dnf module list mariadb
	Last metadata expiration check: 1:07:29 ago on Tue 01 Nov 2022 05:51:34 PM UTC.
	AlmaLinux 8 - AppStream
	Name                      Stream                     Profiles                                        Summary
	mariadb                   10.3 [d]                   client, galera, server [d]                      MariaDB Module
	mariadb                   10.5 [e]                   client, galera, server [d] [i]                  MariaDB Module

	Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled


De la salida, la versión predeterminada es MariaDB 10.3. Sin embargo, instalaremos 10.5, que es el último en el repositorio.
Para que esto suceda, reinicie el módulo MariaDB de la siguiente manera::

	dnf module reset mariadb

Luego instale la última versión de MariaDB usando el comando::

	dnf module install mariadb:10.5

Una vez completado, asegúrese de iniciar el servicio MariaDB.::

	systemctl start mariadb

	systemctl enable mariadb

	systemctl status mariadb

La configuración predeterminada de MariaDB es débil y representa un riesgo potencial de seguridad para el servidor. Como tal, daremos un paso más y fortaleceremos MariaDB. Ejecute el script que se muestra::

	mysql_secure_installation

Se le guiará a través de un par de indicaciones. Asegúrese de establecer primero la contraseña de root.

Para el resto de las indicaciones, responda 'Y' para ajustarlo a la configuración recomendada. Esto implica eliminar usuarios anónimos, bloquear el inicio de sesión raíz remoto y eliminar la base de datos de prueba.

Para iniciar sesión en su servidor de base de datos, ejecute el comando.::

	mysql -u root -p

Instalar PHP 8 en AlmaLinux
-------------------------

El componente final de la pila LAMP que vamos a instalar es PHP. Este es un lenguaje de secuencias de comandos del lado del servidor para el desarrollo de aplicaciones web dinámicas.

	dnf module list php la última versión que ofrece el repositorio de AppStream es PHP 7.4::

	dnf module list php
	Last metadata expiration check: 1:19:55 ago on Tue 01 Nov 2022 05:51:34 PM UTC.
	AlmaLinux 8 - AppStream
	Name                   Stream                    Profiles                                    Summary
	php                    7.2 [d]                   common [d], devel, minimal                  PHP scripting language
	php                    7.3                       common [d], devel, minimal                  PHP scripting language
	php                    7.4 [e]                   common [d], devel, minimal                  PHP scripting language
	php                    8.0                       common [d], devel, minimal                  PHP scripting language

	Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled



Sin embargo, si desea instalar la última versión de PHP, debe instalar el repositorio de Remi. Este es un repositorio de terceros que proporciona las últimas versiones de PHP.

Instale el repositorio de Remi en AlmaLinux de la siguiente manera::

	dnf install http://rpms.remirepo.net/enterprise/remi-release-8.rpm

Una vez más, enumere los módulos de PHP que se ofrecen y esta vez obtendrá el repositorio de Remi en la lista con los módulos de PHP que se ofrecen::

	dnf module list php

En el momento de escribir esta guía, la última versión de PHP es PHP 8.1, que es una Release Candidate. Este es el equivalente a una versión Beta y solo debe usarse con fines de prueba y no de producción::

Para instalar el módulo PHP más reciente, restablezca el módulo PHP predeterminado y habilite el módulo PHP 8.1 de la siguiente manera::

	dnf module reset php
	dnf module enable php:remi-8.1

Finalmente, instale PHP y otros módulos de PHP de su preferencia como se muestra::

	dnf install php php-common php-cli php-mbstring php-xml php-zip php-mysqlnd php-opcache php-curl php-intl php-gd

Cuando se complete la instalación, verifique la versión de PHP instalada.::

	php -v

Además de eso, puede probar PHP en el navegador creando primero un archivo PHP de prueba como se muestra::

	vi /var/www/html/info.php
	<?php
	phpinfo();
	?>

Guarde los cambios y salga del archivo. Reinicie el servidor web Apache::

	systemctl restart php7.3-fpm
	systemctl restart httpd

Verificamos::

	http://server-ip/info.php
	OR
	http://your-domain.com/info.php






