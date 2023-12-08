Política de contraseñas en Linux con PAM y Cracklib
======================================================

Instalación Debian
-----------------------

En el caso de Debian debemos de instalar la librería::

	sudo apt-get install libpam-cracklib

Instalación Fedora
------------------------

En las distribuciones Centos, RedHat y Fedora el módulo PAM Cracklib viene por defecto en la instalación, por lo que no tendremos que hacer nada.

Archivos implicados
--------------------

Los siguientes archivos serán los implicados en la configuración.

Debian::

	/etc/pam.d/common-password: en donde se configuran las reglas a aplicar sobre las contraseñas.
	/etc/login.defs: en donde se define el periodo de expiración de las contraseñas.
	Fedora
	/etc/security/pwquality.conf: en donde se configuran las reglas a aplicar sobre las contraseñas.
	/etc/pam.d/system-auth: indicaremos las últimas contraseñas, las cuales no serán reutilizables.
	/etc/login.defs: en donde se define el periodo de expiración de las contraseñas.
	
Configuración

Vamos a aplicar la configuración a los distintos sistemas con el fin de conseguir la siguiente política::

	Longitud mínima de 10 caracteres.
	Al menos un valor en mayúscula.
	Al menos un valor en minúscula.
	Al menos un carácter numérico.
	Al menos un carácter especial.
	No se pueden repetir las 5 últimas contraseñas.
	Que al menos cambien 3 caracteres en la nueva contraseña con respecto a la anterior.
	Bloquear al introducir la credencial 3 veces erróneamente.
	Caducidad cada 90 días.
	Que advierta 15 días antes de la caducidad.
	Valores positivos y negativos

Como veremos en los siguietnes apartados, los valores de configuración pueden ser positivos o negativos.

Positivos: deben de contener el número de caracteres indicados en su valor.
Negativos: deben de contener al menos el número de caracteres indicados en su valor.
Por ejemplo un valor de 2 para la configuración de caracteres mayúsculas quiere decir que dos valores han de ser mayúsculas, y un valor de -2 quiere decir que al menos dos deben ser mayúsculas.

Debian
---------

En Debian configuramos estos parámetros de la siguiente forma.

Editamos el archivo /etc/pam.d/common-password para configurar la complejidad de las contraseñas.

Agregamos los siguientes valores a la línea que contiene pam_cracklib.so::

	password        requisite                       pam_cracklib.so retry=3 minlen=10 difok=3 lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1
	
También agregamos el parámetro remember a la línea que contiene pam_unix.so::

	password        [success=1 default=ignore]      pam_unix.so obscure use_authtok try_first_pass sha512 remember=5
	
Ahora editamos el archivo /etc/login.defs para configurar los parámetros temporales de caducidad y advertencia::

	PASS_MAX_DAYS 90
	PASS_MIN_DAYS 0
	PASS_WARN_AGE 15
	
	
Fedora
-------------

Primero configuramos los valores de complejidad para las contraseñas, editando el archivo /etc/security/pwquality.conf y agregando los valores que por defecto están comentados::

	retry=3
	minlen=10
	difok=3
	lcredit=-1
	credit=-1
	dcredit=-1
	ocredit=-1
	
Luego agregamos el número total de últimas contraseñas que serán recordadas para no reutilizar, editando el archivo /etc/pam.d/system-auth y agregando el parámetro remember a la siguiente línea::

	password suficient        pam_unix.so sha512 shadow nullok try_first_pass use_authok remember=5
	
Y para finalizar editamos el archivo /etc/login.defs para configurar los parámetros temporales de caducidad y advertencia::

	PASS_MAX_DAYS 90
	PASS_MIN_DAYS 0
	PASS_WARN_AGE 15
	
Significado de los valroes
-----------------------------

Estos valores agregados en la complejidad quieren decir:

	retry: número de intentos consecutivos con credencial fallida.
	minlen: longitud mínima de la contraseña.
	difok: número de caracteres distintos con respecto a la contraseña anterior.
	lcredit: número de caracteres minúscula.
	ucredig: número de caracteres mayúscula.
	dcredit: número de caracteres numéricos.
	ocredit: número de caracteres especiales.
	remember: el total de las últimas contraseñas que no se podrán reutilizar.
	
Sobre los valores temporales::

	PASS_MAX_DAYS: máximo número de días en los que la contraseña será válida desde el último cambio.
	PASS_MIN_DAYS: indica el número de días que han de transcurrir entre un cambio de contraseña y el siguiente.
	PASS_WARN_AGE: indica desde cuando se comenzará a mostrar el mensaje de advertencia previo a la caducidad.
	
Consideraciones importantes
------------------------------

La configuración de los límites de tiempo solo aplica a los nuevos usuarios creados posteriormente a la aplicación de esta política, por lo que los usuarios ya existentes no se verán afectados. Para aplicarle los valores configurados debemos de utilizar manualmente el comando que se muestra en el siguiente apartado.

Esto mismo sucede si en un momento determinado cambiamos la política de tiempo, por ejemplo para que ampliar o disminuir la caducidad de la constraseña.

Es posible que tengamos que configurar un script para actuar y aplicar los valores a todos los ususarios del sistema, o al menos sobre los que queramos actuar.

Sin embargo, los cambios en las característicias de la contraseña aplican en el momento en el que guardamos el archivo, y es extensible a todos los usuarios aunque ya existieran.

Comprobación con el comando CHAGE
Para comprobar los valores aplicados debemos de usar el comando chage::

	$ sudo chage -l test

	Último cambio de contraseña                    :abr 15, 2021
	La contraseña caduca                    : jul 14, 2021
	Contraseña inactiva                    : nunca
	La cuenta caduca                        : nunca
	Número de días mínimo entre cambio de contraseña        : 0
	Número de días máximo entre cambio de contraseña        : 90
	Número de días de aviso antes de que caduque la contraseña    : 15
	
Si quieres aplicarlo a un usuario creado anterior a los cambios estos no tendrán efecto::

	$ sudo chage -l default_user

	Último cambio de contraseña                    :abr 16, 2021
	La contraseña caduca                    : nunca
	Contraseña inactiva                    : nunca
	La cuenta caduca                        : nunca
	Número de días mínimo entre cambio de contraseña        : 0
	Número de días máximo entre cambio de contraseña        : 99999
	Número de días de aviso antes de que caduque la contraseña    : 7
	
Para ello debemos de lanzar el comando manualmente::

	chage default_user -W 15 -M 90 -m 0
	
En este los parámetros son::

	-W: tiempo en el que se muestra la advertencia previo a la caducidad, en días.
	-M: tiempo máximo de caducidad de la contraseña, en días.
	-m: inúmero de días que han de transcurrir entre un cambio de contraseña y el siguiente.
	
Cambio de contraseña no válido
-------------------------------

Si intentamos cambiar la contraseña sin cumplir todos los parámetros configurados en los apartados anteriores se nos mostrarán errores impidiendo ejecutar la acción::

	$ passwd

	Cambiando la contraseña de test.
	Current password: 
	Nueva contraseña: 
	CONTRASEÑA INCORRECTA: Es demasiado corta.
	Nueva contraseña: 
	CONTRASEÑA INCORRECTA: No contiene suficientes caracteres DIFERENTES.
	Nueva contraseña: 
	CONTRASEÑA INCORRECTA: Es demasiado simple/sistemática.
	passwd: Se ha agotado el número máximo de reintentos para el servicio
	passwd: no se ha cambiado la contraseña