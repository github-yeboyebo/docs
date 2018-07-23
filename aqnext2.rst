AQNext en Git
=============

.. toctree::
   :maxdepth: 2

Nuevo repositorio
-----------------

1. Crear repositorio tipo 'Bare' en la nube::

	sudo git init --bare /home/www/git/aqnext/__nuevoRepo__.git

2. Cambiar permisos y propietario del repositorio::

	sudo chmod -Rf u+w /home/www/git/aqnext/__nuevoRepo__.git/
	sudo chown -R git:git /home/www/git/aqnext/__nuevoRepo__.git/

3. Bajar el repositorio en local::

	git clone git@151.80.174.89:/home/www/git/aqnext/__nuevoRepo__.git /path/copia/de/trabajo

4. Copiar o crear los nuevos ficheros dentro de nuestra copia de trabajo y confirmar los cambios::

	cd /path/copia/de/trabajo
	Copiar o crear cambios
	git add .
	git commit -m "Commit Inicial"

5. Subir los cambios al repositorio (en la rama master)::

	git push origin master


Hacer cambios en repositorio
----------------------------

1. Bajamos la funcionalidad actual::

	Si es la primera vez que la bajamos:
		git clone git@151.80.174.89:/home/www/git/aqnext/__repo__.git /path/copia/de/trabajo

	Si ya la tenemos:
		cd /path/copia/de/trabajo
		git pull

2. Realizamos los cambios y subimos::

	cd /path/copia/de/trabajo
	Realizar cambios
	git add (a los ficheros que no estén bajo el control de versiones)
	git commit __fichero__ -m "Cambio de logo en botón imprimir del masterfacturascli"
	git push origin master
