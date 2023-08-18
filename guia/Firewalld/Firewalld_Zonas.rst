

Trabajando con las Zonas
=======================

Mostrar todas las zonas, la que tenga la palabra (active) es la que esta funcionando::

	# firewall-cmd --list-all-zones



Mostrara algo como esto::

	internal
	  target: default
	  icmp-block-inversion: no
	  interfaces:
	  sources:
	  services: dhcpv6-client mdns samba-client ssh
	  ports:
	  protocols:
	  masquerade: no
	  forward-ports:
	  source-ports:
	  icmp-blocks:
	  rich rules:


	public (active)
	  target: default
	  icmp-block-inversion: no
	  interfaces: eno16780032
	  sources:
	  services: dhcpv6-client ssh
	  ports:
	  protocols:
	  masquerade: no
	  forward-ports:
	  source-ports:
	  icmp-blocks:
	  rich rules:


	trusted
	  target: ACCEPT
	  icmp-block-inversion: no
	  interfaces:
	  sources:
	  services:
	  ports:
	  protocols:
	  masquerade: no
	  forward-ports:
	  source-ports:
	  icmp-blocks:
	  rich rules:
 

Para ver una zona en especifico::

	# firewall-cmd --list-all --zone=internal

 
La información de cada una de las zonas estan listadas en un .xml en /usr/lib/firewalld/zones/ o /etc/firewalld/zones/. 
El archivo /usr/lib/firewalld/zones/ no debe ser editado. 
Cuando un cambio se aplica a una zona el .xml sera copiado desde /usr/lib/firewalld/zones/ hacia /etc/firewalld/zones/. 
El .xml en /etc/firewalld/zones/ si puede ser editado. Si el /etc/firewalld/zones/ no tiene los archivos para una zona,  esto significa que no se han aplicado los cambios.

La zona por default puede ser  cambiada usando --set-default-zone=zone_name ::

	# firewall-cmd --set-default-zone=dmz
 

O modificar el archivo /etc/firewalld/firewalld.conf::

	DefaultZone=dmz
 

Tambien pude agregar ZONE=zone_name en el archivo /etc/sysconfig/network-scripts/ifcfg-xxxxxxx ::

	ZONE=public
 


--list-ports
--list-protocols
--info-service=service_name
--query-masquerade