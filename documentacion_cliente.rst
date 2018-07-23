
Crear documentación de cliente
====================================

.. toctree::
   :maxdepth: 2

Crear un nuevo manual 
----------------------

Prerrequisitos
^^^^^^^^^^^^^^^

Tener los permisos necesarios para copiar una carpeta en /home/manuales.

Contexto 
^^^^^^^^^

Los manuales permiten a clientes y desarrolladores acceder directamente a la documentación oficial de sus personalizaciones. Esto evita malentendidos y ahorra tiempo de soporte.

Pasos 
^^^^^^

* Ve a la carpeta de repositorios *bare* y copia el repositorio template.git con el nombre de tu manual::
	
	cd /home/manuales
	sudo cp -R template.git mi_manual.git
	cd mi_manual.git
	sudo chgrp -R sudo .
	sudo chmod -R g+rwX .

Resultado 
^^^^^^^^^^

Ya tenemos listo nuestro repositorio para el nuevo manual.

Más 
^^^^

Puedes comenzar a :ref:`trabajar_manual`

En caso de que tu manual sea de cliente y requiera de autenticación para verlo, debes :ref:`proteger_carpeta_cliente`

.. _trabajar_manual:

Trabajar con un manual 
-----------------------

Prerrequisitos
^^^^^^^^^^^^^^^

Conocer GIT a nivel básico.

Contexto 
^^^^^^^^^

Documentar correctamente un manual evita malentendidos y ahorra tiempo de soporte.

Pasos 
^^^^^^

Trabajaremos con los manuales en una carpeta *manuales* de nuestro home. Esta carpeta contendrá una subcarpeta por cada manual.

Si ya tienes creada la carpeta del manual salta los primeros pasos.

* Crea, si no la tienes ya, una carpeta *manuales* en tu home, y sitúate allí::

	cd
	mkdir manuales

* Si no tienes ya el repositorio en tu copia local, crea la carpeta *mi_manual* con::

	cd manuales
	git clone ssh://antonio@151.80.174.89:/home/manuales/mi_manual.git
	cd mi_manual

* Si ya tienes el repositorio en tu copia local, asegúrate de trabajar sobre la última versión::

	cd manuales/mi_manual
	git pull

* Edita los ficheros y añadelos al repositorio con *git add* y *git commit*.

* Construye el manual HTML::

	cd source
	make html

* Puedes ver cómo queda tu documentación con un navegador en *$HOME/manuales/mi_manual/build/index.html*.

* Cuando esté lista, sube tu documentación al repositorio (asegúrate de haber hecho commit de todos tus ficheros) con::

	git push

.. _proteger_carpeta_cliente:

Proteger la carpeta del cliente
--------------------------------

Si la carpeta es de un cliente debes protegerla con usuario y password.

#. Crea un fichero de texto llamado *.htaccess* con el siguiente contenido::

	AuthName "Area restringida"
	AuthType Basic
	AuthUserFile /home/www/manuales/[CLIENTE]/.htpasswd
	require valid-user

#. Crea el fichero de contraseñas *.htpasswd* con::

	htpasswd -c .htpasswd [USUARIO]

#. Introduce la contraseña y confírmala.

#. Si necesitas crear alguna contraseña más, repite la operación sin la opción *- c* ::

	htpasswd .htpasswd [USUARIO]

#. Cambia los permisos::

	chmod 644 .htaccess
	chmod 644 .htpasswd

#. Mueve los ficheros a la carpeta de la web que contiene la documentación::

	mv .htaccess /home/www/manuales/[CLIENTE]
	mv .htpasswd /home/www/manuales/[CLIENTE]

