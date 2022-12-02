DNF Package Manager
=======================

DNF es el sistema de administración de paquetes predeterminado en la mayoría de las distribuciones de Linux basadas en RPM ahora.

Buscando un paquete
+++++++++++++++++++++

Para buscar un paquete instalado o disponible en todos los repositorios, utilizan el::

  dnf search package_name
  
Listado de paquetes
+++++++++++++++++++++

Para ver una lista de todos los paquetes instalados y disponibles::

  dnf list all
  
Para enumerar todos los paquetes instalados::

  dnf list installed
  
Para enumerar todos los paquetes disponibles::

  dnf list available
  
Por ejemplo, para enumerar todos los paquetes MySQL que están disponibles::

  dnf list available "mysql*"
  
Visualización de información del paquete
----------------------------------------------

Para mostrar información sobre un paquete::

  dnf info <em>package_name
  
Instalar paquetes
+++++++++++++++++++

Para instalar un solo paquete y todas sus dependencias no instaladas::

  dnf install package_name
  
Instalación de un paquete por su nombre de archivo binario
--------------------------------------------------------------

Si conoce el nombre del archivo binario para instalar, pero no el nombre del paquete, incluso eso es suficiente para instalar el paquete correspondiente.

Por ejemplo, si ingresa::

  dnf install /usr/sbin/named
  
Encontrar qué paquete posee un archivo
+++++++++++++++++++++++++++++++++++++++++

Si conoce el nombre del archivo y desea encontrar qué paquete lo proporciona (instalado o no), use::

  dnf provides file_name
  
Eliminar un paquete
+++++++++++++++++++++

Para eliminar un paquete::

  dnf remove package_name

Managing package groups
+++++++++++++++++++++++++++

Un grupo de paquetes es una colección de paquetes que están estrechamente relacionados y tienen un propósito común. La instalación de un grupo de paquetes instala un conjunto de paquetes, lo que ahorra mucho tiempo.

Lista de todos los grupos de paquetes
----------------------------------------

Para enumerar todos los grupos de paquetes::

  dnf group list
  
Enumere todos los paquetes de un grupo
-------------------------------------------

Para enumerar todos los paquetes en un grupo::

  dnf group info package_name
  
Instalación de un grupo de paquetes
------------------------------------

La instalación de un grupo instalará todos los paquetes de un grupo. Para instalar un grupo de paquetes, use::

  dnf group install package_group_name
  
Eliminar un grupo de paquetes
-----------------------------------

Para eliminar un grupo de paquetes::

  dnf remove package_name
  
Actualizar todos los paquetes
++++++++++++++++++++++++++++++++

Para actualizar todos los paquetes y sus dependencias, puede usar::
  
  dnf upgrade
  
Actualizar un solo paquete
----------------------------

Para actualizar un solo paquete, ejecute::

  dnf upgrade package_name
