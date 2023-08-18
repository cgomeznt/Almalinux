

Como abrir el 80/tcp con firewall-cmd::

	# firewall-cmd --zone=public --add-port=80/tcp
	
	# firewall-cmd --permanent --zone=public --add-port=80/tcp
	
	# firewall-cmd --reload
	
Como abrir un servicio con firewall-cmd::

	# firewall-cmd --zone=public --add-service=http

	# firewall-cmd --permanent --zone=public --add-service=http
	
	# firewall-cmd --reload
	
	
Como cerrar el 80/tcp con firewall-cmd::

	# firewall-cmd --permanent --remove-port=80/tcp

	# firewall-cmd --zone=public --remove-port=80/tcp
	
	# firewall-cmd --permanent --zone=public --remove-port=80/tcp
	
	# firewall-cmd --reload
	
Como cerrar un servicio con firewall-cmd::

	# firewall-cmd --zone=public --remove-service=http

	# firewall-cmd --permanent --zone=public --remove-service=http
	
	# firewall-cmd --reload
	
Whitelist an IP Address

Whitelist o permitir el acceso desde un rango de IP o una IP, podemos decirle al firewall el origen en el cual confiamos::

	# firewall-cmd --permanent --add-source=10.140.24.167

	# firewall-cmd --permanent --add-source=192.168.1.0/24
	
	# firewall-cmd --reload
	
Remover IP del Whitelist

	# firewall-cmd --permanent --remove-source=10.140.24.167
	
	# firewall-cmd --reload

Block IP Address

Como la herramienta firewall-cmd se usa principalmente para abrir o permitir el acceso, se necesitan rich-rule para bloquear una IP. 
Las rich-rule tienen una forma similar a la forma en que se escriben las reglas de iptables::

	# firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='10.133.0.125' reject"
	
	# firewall-cmd --reload

Puede volver a usar la notación CIDR y también bloquear un rango de direcciones IP::

	# firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.1.0/24' reject"
	
	# firewall-cmd --reload


Lista blanca de una dirección IP para un puerto específico (reglas más completas)

Tenemos que volver a iptables y crear otra rich-rule; sin embargo, estamos utilizando la declaración de aceptación al final para permitir el acceso de IP, 
en lugar de rechazar su acceso::

	# firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.140.24.167" port protocol="tcp" port="80" accept'
	
	# firewall-cmd --reload

Eliminación de una rich-rule

Para eliminar una rich-rule, use la opción --remove-rich-rule, pero debe especificar completamente qué regla se eliminará, 
por lo que es mejor copiar y pegar la regla completa, en lugar de intentar escribirla toda desde memoria::

	# firewall-cmd --permanent --remove-rich-rule='rule family="ipv4" source address="10.140.24.167" port protocol="tcp" port="3306" accept'
	
	# firewall-cmd --reload

Viewing Firewall Rules::

	firewall-cmd --list-all


Salvar las reglas aplicadas del Firewall::

	firewall-cmd --reload

=================================================================

Abrir un puerto unicamente para las direcciones en Source
===========================================================


firewall-cmd --permanent --zone=public --add-rich-rule='
  rule family="ipv4"
  source address="10.133.0.0/24"
  port protocol="tcp" port="4443" accept'


firewall-cmd --permanent --zone=public --add-rich-rule='
  rule family="ipv4"
  source address="10.133.0.0/24"
  port protocol="tcp" port="10050" accept'


firewall-cmd --permanent --zone=public --add-rich-rule='
  rule family="ipv4"
  source address="10.100.0.0/24"
  port protocol="tcp" port="4443" accept'

firewall-cmd --reload && firewall-cmd --list-all

