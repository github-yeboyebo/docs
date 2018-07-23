
Documentar con Sphinx
====================================

.. toctree::
   :maxdepth: 2

Crear el entorno local
-----------------------------

#. Creamos una carpeta, y dentro de esta una carpeta build::
	
	mkdir doc
	cd doc
	mkdir build

#. Obtenermos la carpeta source de svn::

	svn co http://151.80.174.89/svn/yeboyebo/doc/source

con esto tenemos en nuestra carpeta de documentación las subcarpetas build y source.

Crear una nueva página
-----------------------------

#. En la carpeta source, creamos el correspondiente archivo .rst

#. Inclimos su nombre (sin .rst) en el toctree de su archivo padre::

	.. toctree::
   		:maxdepth: 2

   		mi_nuevo_archivo

.. _usar-sphinx--documentar:

Documentar
-----------------------------

El título de la página debe estar marcado como sección::

	Este es mi Título
	=================

Dentro de la página incluiremos las subsecciones que queramos::

	Esta es mi Subsección
	---------------------

Texto::

	Esto es *cursiva*, esto **negrita**,

	esto ``a = b`` es código, esto un subíndice :sub:`2`.

Resultados:

	Esto es *cursiva*, esto **negrita**, esto ``a = b`` un ejemplo de código, esto un subíndice :sub:`2`.

Listas::

	* Esto es una lista desordenada

	* De dos elementos (no olvides el espacio entre líneas)

	#. Esto es una lista ordenada

	#. De dos elementos (no olvides el espacio entre líneas)

Resultado:

	* Esto es una lista desordenada

	* De dos elementos (no olvides el espacio entre líneas)

	#. Esto es una lista ordenada

	#. De dos elementos (no olvides el espacio entre líneas)

Enlaces::

	`Enlace externo <http://sphinx-doc.org/rest.html#rst-primer>`_

	Enlace interno a :ref:`usar-sphinx--documentar`

Resultado:

	`Enlace externo <http://sphinx-doc.org/rest.html#rst-primer>`_

	Enlace interno a :ref:`usar-sphinx--documentar`

La documentación completa del lenguaje reStructuredText está `aquí <http://sphinx-doc.org/rest.html#rst-primer>`_

Construir la Documentación
--------------------------

Para construir la documentación html nos situamos en la carpeta source y ejecutamos::

	make html

en el directorio que contiene el archivo *Makefile*. Esto actualiza el directorio build. **Atención a los warnings que puedan aparecer en la consola**.

Podemos comprobar cómo queda la documentación accediendo en el navegador a http://mi_ruta_local/build/html

Publicar la documentación
--------------------------

Para subir a svn y publicar la documentación, desde la carpeta source local::

	svn up
	svn status (resolvemos los conflictos, si los hay)
	svn commit -m "Indica qué has documentado"

En el servidor::

	cd /home/www/doc/source
	svn up
	make html

Podemos comprobar cómo queda la documentación accediendo en el navegador a http://151.80.174.89/doc/build/html/

Reglas de documentación Yeboyebo
--------------------------------

* Los nombres de los ficheros rst tendrán el formato::

	nombre_de_fichero.rst

* La cabecera del documento será una sección (subrayado con =) y podremos crear subniveles subrayando con -, ^ y " según consideremos.

* Usaremos la negrita para destacar y la cursiva para indicar nombres. Por ejemplo "El directorio *source*..."

* Siempre que sea posible, expresaremos la descripción de un proceso en una secuencia de pasos concisos.

* Evitaremos duplicar información que ya está documentada externamente, salvo si es algo que se va a usar mucho. Si no es así basta con incluir enlaces a las páginas que contienen la información.

* Aunque podemos crear todos los niveles que queramos en el árbol de documentos, por ahora vamos a tener únicamente el documento índice y un segundo nivel con todos los demás documentos. A medida que avancemos con la documentación iremos estructurándola mejor.

* En el caso de documentar trucos o recetas de código podemos incluir enlaces al svn donde haya ejemplos de lo que estamos documentando.