REMI Repository en Rocky Linux 8 | AlmaLinux 8
=================================================

Comprender los repositorios REMI
------------------------------------

A Remi Collect, estableció y mantiene REMI, que es un repositorio de uso gratuito. El propósito principal del repositorio REMI es brindar las versiones más actualizadas de la pila de PHP, así como otros paquetes de software, a Fedora y Enterprise Linux (Rocky Linux, AlmaLinux, RHEL, CentOS, Oracle, Scientific Linux ) SO.

Repositorio REMI-seguro
---------------------------
Este repositorio es para personas que desean priorizar la distribución y los paquetes EPEL sin tener que reemplazarlos (por ejemplo, para mantener el soporte de Red Hat), pero que requieren algunas extensiones adicionales a las que aún no se puede acceder en EPEL. Este repositorio no es intrusivo y es extremadamente seguro, ya que no reemplaza ni choca con ninguna distribución o paquete EPEL. Algunas bibliotecas requeridas por estas adiciones también están disponibles en el repositorio. Los paquetes son idénticos a los de “remi”, lo que indica que este es un subconjunto del repositorio principal.

La mayoría de los paquetes de REMI no se encuentran en el repositorio predeterminado del sistema operativo. Si existen, lo más probable es que estén obsoletos y carezcan de características más nuevas. En Rocky Linux 8 | AlmaLinux 8 Linux distribución, esta instrucción le mostrará cómo habilitar REMI Repository.


Habilite el repositorio REMI en Rocky Linux 8 | AlmaLinux 8
-------------------------------------------------------------

Antes de instalar el repositorio REMI, primero debemos activar el popular repositorio Extra Packages for Enterprise Linux (EPEL) en nuestro servidor Rocky Linux 8 o AlmaLinux 8::

  dnf install epel-release
  
Despues de activar EPEL, actualizar el sistema::

  dnf update
  
  dnf upgrade
  
Para instalar el repositorio REMI en Rocky Linux 8 | AlmaLinux 8::

  dnf install http://rpms.remirepo.net/enterprise/remi-release-8.rpm
  
Verficamos los repositorios habiliados::

  dnf repolist
  repo id                                    repo name
  appstream                                  AlmaLinux 8 - AppStream
  baseos                                     AlmaLinux 8 - BaseOS
  epel                                       Extra Packages for Enterprise Linux 8 - x86_64
  extras                                     AlmaLinux 8 - Extras
  remi-modular                               Remi's Modular repository for Enterprise Linux 8 - x86_64
  remi-safe                                  Safe Remi's RPM repository for Enterprise Linux 8 - x86_64
  zabbix                                     Zabbix Official Repository - x86_64
  zabbix-non-supported                       Zabbix Official Repository non-supported - x86_64
  
  El repositorio REMI se usa de las siguientes maneras en Rocky Linux 8 | AlmaLinux 8::
  
  Lista de paquetes disponibles en el Repositorio REMI::
  
    dnf --disablerepo="*" --enablerepo="remi" list available
    
  También puede usar el siguiente comando para enumerar los paquetes disponibles en el repositorio REMI-safe::
  
    dnf --disablerepo="*" --enablerepo="remi-safe" list available
    
  Instalación de paquetes desde repositorios Remi / Remi-safe
  --------------------------------------------------------------
  
Ejemplo: Instalación de módulos PHP
Debe habilitar los paquetes del repositorio de REMI para poder utilizarlos. Veamos una lista de módulos que proporcionan php usando::

  dnf module list php
  Last metadata expiration check: 2:24:12 ago on Fri 02 Dec 2022 08:40:57 PM UTC.
  AlmaLinux 8 - AppStream
  Name               Stream                    Profiles                                    Summary
  php                7.2 [d]                   common [d], devel, minimal                  PHP scripting language
  php                7.3                       common [d], devel, minimal                  PHP scripting language
  php                7.4                       common [d], devel, minimal                  PHP scripting language
  php                8.0                       common [d], devel, minimal                  PHP scripting language

  Remi's Modular repository for Enterprise Linux 8 - x86_64
  Name               Stream                    Profiles                                    Summary
  php                remi-7.2                  common [d], devel, minimal                  PHP scripting language
  php                remi-7.3                  common [d], devel, minimal                  PHP scripting language
  php                remi-7.4 [e]              common [d] [i], devel, minimal              PHP scripting language
  php                remi-8.0                  common [d], devel, minimal                  PHP scripting language
  php                remi-8.1                  common [d], devel, minimal                  PHP scripting language
  php                remi-8.2                  common [d], devel, minimal                  PHP scripting language
  

En el resultado anterior, hemos visto los módulos php proporcionados por el repositorio REMI. Ahora, podemos instalar de la siguiente manera.

Aquí elegimos instalar PHP 7.4 para nuestra demostración::

  dnf module install php:remi-7.4
  
Confirme la versión de php instalada::

  php -v
  PHP 7.4.33 (cli) (built: Oct 31 2022 10:36:05) ( NTS )
  Copyright (c) The PHP Group
  Zend Engine v3.4.0, Copyright (c) Zend Technologies
      with Zend OPcache v7.4.33, Copyright (c), by Zend Technologies

Si queremos instalar otros modulos de php como; php-curl php-mbstring php-opcache php-xml php-gd php-intl php-xmlrpc php-soap php-pecl-zip php-mysql, solo hacemos::

  dnf instrall php-curl php-mbstring php-opcache php-xml php-gd php-intl php-xmlrpc php-soap php-pecl-zip php-mysql
  
El comando anterior por defecto la versión sera de la 7.4, porque es el repo que se habilito con el dnf module.
  

Deshabilitar o habilitar el repositorio REMI en Rocky Linux 8 | AlmaLinux 8
----------------------------------------------------------------------------

Instale yum-config-manager, que le permite habilitar y deshabilitar rápidamente un repositorio desde la línea de comandos::

  dnf -y install dnf-utils
  

Habilitar o deshabilitar repositorios temporalmente
-------------------------------------------------

Para evitar que un paquete se actualice, podemos excluir temporalmente un repositorio::

  dnf --disablerepo=remi-safe update
  
  
Habilitar o deshabilitar repositorios de forma permanente
--------------------------------------------------------

Habilitar::

  dnf config-manager --set-enable remi-safe

Deshabilitar::

  dnf config-manager --set-disable remi-safe

Para habilitar el repositorio REMI de forma permanente, edite el archivo /etc/yum.repos.d/remi.repo. Busque la línea habilitada =0 y cámbiela a 1 como se muestra a continuación::

  vi /etc/yum.repos.d/remi.repo
  [remi]
  name=Remi's RPM repository for Enterprise Linux 8 - $basearch
  #baseurl=http://rpms.remirepo.net/enterprise/8/remi/$basearch/
  #mirrorlist=https://rpms.remirepo.net/enterprise/8/remi/$basearch/httpsmirror
  mirrorlist=http://cdn.remirepo.net/enterprise/8/remi/$basearch/mirror
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi.el8
  

El archivo debe guardarse y cerrarse. Para que los cambios surtan efecto, actualice las listas del repositorio::

  dnf update
  dnf repolist
