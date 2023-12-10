El Bloqueo de la cuenta con pam_faillock en RHEL
=================================================================================

Anteriormente utilizabamos **pam_tally** pero luego de la versión REHL 7 esta decrecado, ahora se utiliza **pam_faillock**

En las distribuciones basadas en RedHat este bloqueo se consigue gracias al módulo de PAM denominado “pam_faillock” y en Debian el módulo se llama “pam_tally2”

Bloqueo de cuentas en RedHat

Para el bloqueo de cuentas habrá que añadir las siguientes líneas en los ficheros password-auth y system-auth del directorio /etc/pam.d::

	auth required pam_faillock.so preauth silent audit deny=3 unlock_time=120 
	auth sufficient pam_unix.so nullok try_first_pass 
	auth [default=die] pam_faillock.so authfail audit deny=3 unlock_time=120

	account required pam_faillock.so
	
Donde::

	audit: Habilita los logs para el registro de intentos de Login
	deny=2: Bloquea la cuenta de usuario después de 2 intentos fallidos
	unlock_time=120: Tiempo de bloqueo de la cuenta, en nuestro caso 120 segundos
	silent: No informa al usuario que la cuenta ha sido bloqueada
	
Esta configuración no afecta al usuario root. Para hacerlo añadimos en las dos primeras líneas el parámetro “even_deny_root”.

Para ver el log::

	# tail -f /var/log/secure

Para ver los intentos fallidos de un usuario::

	# faillock --user cgomez
	cgomez:
	When                Type  Source                                           Valid
	2023-12-10 07:48:23 RHOST 192.168.0.1                                          V
	2023-12-10 07:48:31 RHOST 192.168.0.1                                          V
	2023-12-10 07:48:38 RHOST 192.168.0.1                                          V
	
Para resetar los intentos fallidos del usuario::

	# faillock --user cgomez --reset

	

