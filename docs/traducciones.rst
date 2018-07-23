
Traducciones AbanQ
====================================

.. toctree::
   :maxdepth: 2

Ubicación de cadenas a traducir
--------------------------------

+----------------------+-----------------------+-------------------+--------------+
| Contexto UI          | Fichero TS            | Contexto          | Origen       |
+----------------------+-----------------------+-------------------+--------------+
| Áreas funcionales    | idmodulo.xx.ts (>1)   | mainwindow        | idmodulo.mod |
+----------------------+-----------------------+-------------------+--------------+
| Módulos funcionales  | idmodulo.xx.ts        | mainwindow        | idmodulo.mod |
+----------------------+-----------------------+-------------------+--------------+
| Menú funcional       | idmodulo.xx.ts        | idmodulo          | idmodulo.ui  |
+----------------------+-----------------------+-------------------+--------------+
| Pestañas             | idmodulo.xx.ts        | mainwindow        | idmodulo.xml |
+----------------------+-----------------------+-------------------+--------------+
| Campos               | idmodulo.xx.ts        | MetaData          | *.mtd        |
+----------------------+-----------------------+-------------------+--------------+
| Labels, tooltips     | idmodulo.xx.ts        | nombre_ui         | nombre_ui.ui |
+----------------------+-----------------------+-------------------+--------------+
| Mensajes funcionales | idmodulo.xx.ts        | scripts           | *.qs         |
+----------------------+-----------------------+-------------------+--------------+
| Form de conexión     | sys.xx.ts             | FLWidgetConnectDB | (motor)      |
+----------------------+-----------------------+-------------------+--------------+
| Mensajes motor       | sys.xx.ts             | sc | (motor)      |
+----------------------+-----------------------+-------------------+--------------+
	       ??                           _
Ventana  motor	       sys.XX.tr/mainwindow         _
Opciones Menú motor	   sys.XX.tr/sys        _
====================== =========================== ===================

Aquí tienes los |location_link|.

.. |location_link| raw:: html

   <a href="https://drive.google.com/drive/folders/0ByXA76_lEb55bDNUdkI2RE14bDA?usp=sharing " target="_blank">ficheros de actualización de traducciones</a>

Notas generales
-----------------
Para que la traducción de las tildes funcione correctamente, los ficheros deben estar en la codificación iso-8859-1. Para comprobarlo poremos hacer::

	$ file -i flfacttesto.en.tr

y el resultado debe ser::

	$ flfactteso.en.ts: application/xml; charset=iso-8859-1

Los módulos y áreas no se traducen correctamente porque se traducen en el momento de ser introducidos en las tablas de flareas y flmodules. Hay que tocar:

* flloadmodule.qs para no traducir la descripción de módulo y área al cargar un nuevo módulo

* aqapplication.qs para traducir la descripción de módulo y área en el momento de añadirlas al menú de la ventana principal

Traducir módulos
-----------------

#. Descargamos y copiamos, si no existe ya, el fichero de actualización de traducciones update_fun.sh como update.sh en la carpeta translations del módulo::

	$ cd _ruta_modulo_
	$ cp $HOME/Descargas/update_modulo.sh traducciones/update.sh
	$ chmod 744 traducciones/update.sh

#. Desde el módulo de *Mantenimiento de versiones*, formulario *Módulos* pulsamos el botón *Actualizar traducciones*.

#. Seleccionamos el directorio principal del módulo y aceptamos. Esto debe regenerar los ficheros TS.

#. Traducir con linguist. Si algo no se traduce hay que comprobar que lo hemos hecho en el contexto (nodo contexto del .ts) adecuado. La tabla superior nos indica en qué fichero y contexto debe estar cada cadena a traducir en función de su ubicación en la aplicación::

	$ _ruta_a_eneboo/bin/linguist &

#. Para comprobar los cambios es necesario borrar la caché de AbanQ / Eneboo y reiniciar cada vez que recarguemos los módulos

#. Cuando terminamos, antes de subir el módulo, debemos volver a usar el botón *Actualizar traducciones*. Esto soluciona unos problemas con los acentos que surgen al usar el linguist.

#. Subimos el módulo al repositorio de control de versiones de forma manual.

Traducir extensiones
---------------------

#. Vamos a la carpeta del parche

#. Descargamos y copiamos, si no existe ya, el fichero de actualización de traducciones update_extension.sh como update.sh en la carpeta del parche.

#. Desde el mantenimiento de versiones pulsamos el botón *Actualizar traducciones*

#. Traducir.

#. Comprobar las traducciones en *pruebas* mediante el botón *Recargar pruebas*.

#. Borrar caché y volver a cargar el correspondiente módulo.

#. Cuando terminamos, antes de subir el la extensión, debemos volver a usar el botón *Actualizar traducciones*. Esto soluciona unos problemas con los acentos que surgen al usar el linguist.

#. Subimos la funcionalidad desde el módulo de mantenimiento de versiones.

Traducir el motor
---------------------
Por ahora conservaremos las traducciones en una carpeta aparte en svn (http://151.80.174.89/svn/traducciones).

#. Creamos o actualizamos nuestra carpeta de svn de traducciones del motor: *svn co http://151.80.174.89/svn/traducciones / cd traducciones; svn up*

#. Descargamos la última versión de Eneboo (git clone eneboo... + checkout 2.4.5)

#. Copiamos el contenido de nuestra carpeta traducciones en la carpeta translations de src:: cp traducciones/* src/translations/

#. Descargamos y copiamos, si no existe ya, el fichero de actualización de traducciones update_motor.sh como update.sh en la src/translations.

#. Traducir.

#. Borrar caché y rearrancar Eneboo. Lo mejor es tener un eneboo de la misma versión ya compilado y, en la misma línea, lanzar algo así como: *cp (ruta fuente)/src/translations/sys.en.ts (ruta binario)/share/eneboo/translations/; rm -rf .eneboocache/;./(ruta binario)/bin/eneboo &*

#. Cuando terminamos debemos copiar el contenido de la carpeta *src/translations* en nuestra carpeta traducciones de svn y subir al repositorio.
