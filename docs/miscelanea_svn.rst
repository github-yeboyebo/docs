Miscelánea Subversion
=====================

.. toctree::
   :maxdepth: 2

Eliminar todos los ficheros perdidos (símbolo !)
------------------------------------------------

Cuando eliminas ciertos ficheros que estaban marcados para subir, pero no lo hiciste. Este comando elimina todos del repositorio.::

	svn rm $( svn status | sed -e '/^!/!d' -e 's/^!//' )

O más sencillo::

	svn rm $( svn status | grep '^!' | cut -c8- )

Donde:
	- svn status es el listado de los ficheros que han cambiado respecto al repositorio.
	- grep '^!' filtra el listado anterior para los que contengan el símbolo '!' al principio de la línea (de ahí el caracter '^'). (-v para los que no contengan)
	- cut -c8- quita los primeros 8 caracteres para que solamente quede la ruta del fichero.
	- svn rm $( comandos ) ejecuta el rm (borrado) a los ficheros que devuelvan los comandos.

Aplicar SvnIgnore
-----------------

Aplicar el fichero svnignore a todos los ficheros de un directorio.::

	svn propset svn:ignore -R -F .svnignore .
