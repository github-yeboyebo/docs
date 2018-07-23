Aplicacion offline Elganso
===========================

Datos de conexion
-----------------

Servidor de Elganso::

	ssh elganso@5.196.36.250

Dentro de el servidor del Elganso

	* El entorno de la maquina::

		/var/www/web/ERPYB/

	* Directorio de fuentes::

		/var/www/web/ERPYB/src

		Para la aplicacion offline es necesario realizar un link:
			sudo ln -s ../YBWELGANSO/static/YBWELGANSO YBWELGANSO

	* Directorio de estaticos(js y demás)::

		/var/www/web/ERPYB/src/assets/

	* Para cambiar BBDD o añadir nuevas::
		 en /var/www/web/ERPYB/src/YEBOYEBO/local.py (Esto expande/Sobreescribe lo setting en local)

	* Para rearrancar django(Necesario cuando cambias algo de LEGACY)::
		sudo service gunicornERPYB restart

	* Para conectar con psql::
		psql nombrebasedatos -h 172.26.10.14 -U elganso

Aplicacion de YEBOYEBO en svn::

	http://151.80.174.89/svn/web/YEBOYEBO/desarrollo/trunk/



Estructura de ficheros
----------------------


La aplicacion esta en **trunk/YBWELGANSO/** tiene dos partes:

	#. En la carpeta *template/YBWELGANSO/eg_inventarios* estan el archivo index y el manifest que controla el funcionamiento offline.

	#. En la carpeta *static/YBWELGANSO/eg_inventarios* esta el resto de la aplicación.

El direcotorio static a su vez esta formado por:
	
	* bower_components: Librerias externas como angularJS.
	* css: Hojas de estilo
	* fonts.
	* img: imagenes.
	* includes: ficheros HTML
	* sounds: Sonidos de aviso para la PDA.
	* json.
	* js: Aqui estan ubicados los archivos de configuracion de angularJS y la base de datos indexada(indexeddb) entre otros.

Configuracion de almacen local
------------------------------

Cuando carga la aplicacion en la PDA toma como almacen el que este guardado en el campo *egcodalmadefecto* de *flfactalma_general*. Para configurar ese almacen desde el AbanQ de las tiendas tienen que entrar en almacen/configuracion y en la pestaña de ElGanso seleccionar el Almacén local.

Dominios de aplicación
----------------------

El dominio DNS deberia estar creado por flipbird pero la aplicacion de las PDAs tambien debe añadir esos dominios y sobre todo la ubicacion de la base de datos al fichero de configuracion de django.

Para comprobar si una tienda a la aplicacion debemos editar el fichero **/var/www/web/ERPYB/src/YEBOYEBO/local.py** con **vi** podemos buscar con la barra si el dominio ya esta añadido por ejemplo **/aeso**, tanto si hay que añadir un nuevo dominio como editar uno viejo el formato es el siguiente::

        'aeso':{
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'USER': 'elganso',
            'PASSWORD': 'elganso',
            'HOST':'10.46.31.1',
            'PORT':'5432',
            'CONN_MAX_AGE':60,
            'ATOMIC_REQUEST':True,
            'NAME':'ecipintorsorolla',
        },

Los datos que necesitamos cambiar son:

* NAME: nombrebd
* HOST: IP servidor
* clave: La clave del JSON debe ser el codtienda en minusculas.

El formato es JSON, una vez añadido el domino hay que reiniciar el servicio de gunicorn::

		sudo service gunicornERPYB restart

Errores comunes:

* No existe el dominio algo.elganso.com: Se puede comprobar haciendo un ping desde el servidor de PDA al dominio(importante que sea desde el servidor de PDA ya que esta  en la misma red si no el ping nos devuelve la ip del elganso.com)

* No existe el usuario elganso en la Base de datos: Es un error no muy comun pero se ha dado ya un par de veces.

* No hay conexion con el servidor: Si la aplicacion no funciona, el dominio esta añadido y todos los datos son correctos hay que hacer un ping a la IP del servidor para comprobar que hay conexion.

Logs
----

Podemos consultar los logs de la aplicacion en **/var/www/web/ERPYB/log/** aqui tenemos tres ficheros:

* **django.log**: este fichero se utiliza en modo debug de django como la aplicacion de las PDAs ya no esta en modo debug aqui solo se almacenan aquellas url que se introducen mal.

* **gunicornerror.log**: Aqui se almacenaran los errores relacionados con el socket de conexion con NGINX.

* **yebo.log**: Este es el fichero mas importante, aqui se almacenaran los debug de nuestra aplicacion, ademas se registran algunas actividades como la conexion con una base de datos, tenemos dos tipos de log:
	
	* Para indicar la conexion con una base de datos: **YEBOYEBO.YBLUTILS.DbRouter     RESPONDIDA LECTURA BBDD: acen** este nos puede servir para ver sobre que dominio estan trabajando.

	* Los debug que colocemos en nuestro codigo tendran este formato: **DEBUG    YEBOYEBO.LEGACY                0000089440  --Envio-- True**


Manifest
--------

El archivo manifest debe de estar indicado en el *index.html*::

	<html ng-app="App" xmlns="http://www.w3.org/1999/xhtml" manifest="appcache.manifest">

El archivo de manifiesto de caché es un sencillo archivo de texto que contiene los recursos que debe almacenar en caché el navegador para el acceso sin conexión. Cualquier url que no este indicada en el archivo manifest(externa o interna) no sera accesible. Manifest tiene la siguiente estructura::

	CACHE MANIFEST {% load staticfiles %}

	#version 1.1.1 27-11-2015 

	CACHE: 

		Aqui indicaremos las urls que queremos que sean cacheadas, ej:
		{%static "YBWELGANSO/eg_inventarios/includes/envios.html" %}

	NETWORK:
		Aqui indicamos las urls que seran accesibles cuando tengamos conexion a internet.

