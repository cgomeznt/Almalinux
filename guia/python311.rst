Como compilar e instalar las ultimas versiones de Python 3.11, 3.10, 3.9, 3.8, 3.7 
=================================================================================

Actualizar::

  dnf -y update

Instalar las dependencias necesarias::

  dnf -y install wget yum-utils gcc openssl-devel bzip2-devel libffi-devel

Prepara el área de trabajo::

  cd /tmp/
  wget https://www.python.org/ftp/python/3.11.1/Python-3.11.1.tgz
  tar xzf Python-3.11.1.tgz
  cd Python-3.11.1

Compilar Python con static libraries::

  sudo ./configure --prefix=/opt/python311 --enable-optimizations --with-system-ffi --with-computed-gotos --enable-loadable-sqlite-extensions
  sudo make -j "$(nproc)"
  sudo make altinstall

Compilar Python con shared libraries::

  sudo ./configure --prefix=/opt/python311 --enable-optimizations --with-system-ffi --with-computed-gotos --enable-loadable-sqlite-extensions --enable-shared LDFLAGS=\"-Wl,-rpath /usr/local/lib\"
  sudo make -j "$(nproc)"
  sudo make altinstall
  ldconfig

Eliminar archivo comprimido::

  rm /tmp/Python-3.11.1.tgz

Agregar los symbolic links::

  sudo ln -s /opt/python311/bin/python3.11 /opt/python311/bin/python3
  sudo ln -s /opt/python311/bin/python3.11 /usr/bin/python311
  sudo ln -s /opt/python311/bin/python3.11 /usr/bin/python
  sudo ln -s /opt/python311/bin/python3.10 /opt/python311/bin/python
  sudo ln -s /opt/python311/bin/python3.11-config /opt/python311/bin/python-config
  sudo ln -s /opt/python311/bin/pydoc3.11 /opt/python311/bin/pydoc
  sudo ln -s /opt/python311/bin/idle3.11 /opt/python311/bin/idle

Agregar los symbolic links para pip::

  sudo ln -s /opt/python311/bin/pip3.11 /opt/python311/bin/pip3
  sudo ln -s /opt/python311/bin/pip3.11 /opt/python311/bin/pip
  sudo ln -s /opt/python311/bin/pip3.11 /usr/bin/pip

Verificar la instalación::

  /opt/python311/bin/pip -V
  >>> pip 22.3 from /opt/python311/lib/python3.11/site-packages/pip (python 3.11.1)
  /opt/python311/bin/python -V
  >>> Python 3.11.1
  which python311
  >>> /usr/bin/python311
  /usr/bin/python311 -V
  >>> Python 3.11.1
  /opt/python311/bin/python3.11-config --prefix
  >>> /opt/python311
  # test out shipped Python 2.x whether it still ok
  /usr/bin/python -V
  >>> Python 2.7.5
  
Configurando Python virtual environment (venv)
----------------------------------------------

Virtualenv es una herramienta usada para crear un entorno Python aislado. Pero **venv** es un paquete que viene con Python 3. Python 2 no contiene venv. **virtualenv** es una biblioteca que ofrece más funcionalidades que venv. Mira el siguiente enlace para obtener una lista de características que venv no ofrece en comparación con virtualenv::

  pip install virtualenv 

  /opt/python311/bin/python3.11 -m pip install --upgrade pip

  python -m virtualenv python/venv

  # . python/venv/bin/activate
  (venv) [root@localhost ~]#

Consultamos los paquetes de nuestro entorno virtual::

  # pip list
  Package    Version
  ---------- -------
  pip        22.3.1
  setuptools 65.6.3
  wheel      0.38.4


