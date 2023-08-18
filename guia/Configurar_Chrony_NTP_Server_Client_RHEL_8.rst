Configurar Chrony NTP Server en RHEL 8/CentOS 8
=================================================


Chrony es una implementación versátil del Network Time Protocol. Tiene dos programas chrony y chronyd.

**chronyd** es un demonio que se puede iniciar en el momento del arranque

**chronyc** es un programa de interfaz de línea de comandos que se puede usar para monitorear el rendimiento de chronyd y cambiar varios parámetros operativos 
mientras se está ejecutando.

Instalando Chrony NTP
==========================

timedatectl ya viene por default en la instalación. 

Podremos primero necesitar listar los time zones disponibles ::

	# timedatectl list-timezones

Set timezone de  America/Caracas::

	# timedatectl set-timezone America/Caracas

En este link puede encontrar como es el timezone de su preferencia::

	https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

Vemos el status ::

	#  timedatectl status
					   Local time: Thu 2023-08-17 19:43:55 -04
				   Universal time: Thu 2023-08-17 23:43:55 UTC
						 RTC time: Thu 2023-08-17 23:43:55
						Time zone: America/Caracas (-04, -0400)
		System clock synchronized: yes
					  NTP service: active
				  RTC in local TZ: no



Chrony ya viene instalado desde el from package manager::

	# dnf -y  install chrony
	
Habilitamos y iniciamos el servicio::

	systemctl enable chronyd
	systemctl restart chronyd
	systemctl status chronyd	
	
Configurando Chrony NTP
===========================

Despues de la instalación, podemos realizar cambios en **Chrony** en el archivo de configuración  **/etc/chrony.conf**

Una de las configuraciones principales es cambiar el **time server** más cercano de la región. En este link puede encontrar el NTP Atomico correspondiente a VE::

	https://www.ntppool.org/zone/ve

Modificamos el archivo de configuracion con los servidores Atomicos. comentamos la primera linea en donde esta publicado el servidor ntp uburst::

	# vi /etc/chrony.conf
		# Use public servers from the pool.ntp.org project.
		# Please consider joining the pool (http://www.pool.ntp.org/join.html).
		#pool 2.almalinux.pool.ntp.org iburst
		server 0.south-america.pool.ntp.org
		server 1.south-america.pool.ntp.org
		server 2.south-america.pool.ntp.org
		server 3.south-america.pool.ntp.org

Fijamos el NTP syncronización::

	# timedatectl set-ntp true
	
Reiniciamos el **Chrony**::

	# systemctl restart chronyd

Podemos especificar que IP o rango de redes bloquear o permitir el acceso al NTP server::

	# Allow NTP client access from local network.
	allow 192.168.25.0/24
	
Activamos el puerto NTP en el Firewalld::

	# firewall-cmd --add-service=ntp --permanent 
	# firewall-cmd --reload
	# firewall-cmd --list-all
	
Verificamos la sincronización del NTP::

	# chronyc sources
	MS Name/IP address         Stratum Poll Reach LastRx Last sample
	===============================================================================
	^* time.cloudflare.com           3   6    17    51  +3343us[+8069us] +/-   72ms
	^? ptp.aggo-conicet.gob.ar       1   6     5   114    -35ms[  -30ms] +/-  112ms
	^- b.ntp.br                      2   6    17    49    +30ms[  +30ms] +/-  145ms
	^? sa-south-1.clearnet.pw        0   6     0     -     +0ns[   +0ns] +/-    0ns
	


Configure NTP Client
============================

Ahora que tenemos un **Chrony Server** instaldo y configurado en nuestra red, podemos configurar los NTP Client

Set timezone::

	timedatectl set-timezone America/Caracas
	
Instalamos el Chrony::

	dnf -y install chrony
	
Editamos el archivo de configuracion para que utilice nuestro **NTP Server**::

	# sudo vi /etc/chrony.conf
	server 192.168.0.21

Activamos la sincronización del NTP::

	timedatectl set-ntp true

Habilitamos y iniciamos el servicio::

	systemctl enable chronyd
	systemctl restart chronyd
	systemctl status chronyd	
	
Verificamos la sincronización del NTP::

	$ sudo chronyc sources
	 210 Number of sources = 3
	 MS Name/IP address                 Stratum Poll Reach LastRx Last sample               
	 ^+ 192.168.0.21                   2   7   377    61    +12ms[  +12ms] +/-  100ms
	 
Cambiar el Timezone/Date
============================

Verificamos el hostnamectl::

	# hostnamectl
	   Static hostname: srv-semilla
			 Icon name: computer-vm
			   Chassis: vm
			Machine ID: a4311a20b18946c7a5a550e1f0f06920
			   Boot ID: 085f6537e3814423a24527ff8b2f3f58
		Virtualization: vmware
	  Operating System: AlmaLinux 8.8 (Sapphire Caracal)
		   CPE OS Name: cpe:/o:almalinux:almalinux:8::baseos
				Kernel: Linux 4.18.0-477.21.1.el8_8.x86_64
		  Architecture: x86-64

Cambiamos la hora actual
----------------------------

Verificamos el timedatectl::

	#  timedatectl status
				   Local time: Thu 2023-08-17 19:43:55 -04
			   Universal time: Thu 2023-08-17 23:43:55 UTC
					 RTC time: Thu 2023-08-17 23:43:55
					Time zone: America/Caracas (-04, -0400)
				System clock synchronized: yes
				  NTP service: active
			  RTC in local TZ: no
			  

Cambiamos la hora actual – en tanto del system time y del hardware clock::

	# timedatectl set-time HH:MM:SS
	
Ejemplo 10:21pm::

	# timedatectl set-time 22:21:00

Si tiene activo el NTP service, producira una falla con el siguiente error::

	“Failed to set time: NTP unit is active“.

Por defecto, el sistema esta configurado a utilizar UTC. Podemos forzar que use la hora local (No recomendado)::

	# timedatectl set-local-rtc true
	
Cambiamos la Fecha actual
-----------------------------

La fecha actual la podemos cambiar ejecutando la siguietne sintaxis de comando::

	# timedatectl set-time "yyyy-MM-dd hh:mm:ss"
	
Cambiando el Time Zone
------------------------

Podremos primero necesitar listar los time zones disponibles ::

	# timedatectl list-timezones

Set timezone de  America/Caracas::

	# timedatectl set-timezone America/Caracas
	
Con el siguiente comando forzamos que utilice un NTP remoto, el cual debe ya estar configurado en su archivo de configuración::

	timedatectl set-ntp yes