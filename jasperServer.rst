jasperServer
============


Instalar JasperServer
---------------------

#. Descargar JasperServer version 6.2.1 de::

	http://community.jaspersoft.com/project/jasperreports-server

	Usario: juanma.yeboyebo@gmail.com
	Contraseña: c0......

#. Se bajara un archivo *.run* sobre el que podemos hacer::

	chmod +x archivo.run
	./archivo.run

#. Instalar jasperServer

#. Si necesitamos iReport descargar la version 5.5.0::

	http://community.jaspersoft.com/project/ireport-designer/releases

#. Se ejecuta desde iReport/bin/ireport


Para empezar a trabajar con jasperserver abrimos en el navegador la url::

	127.0.0.1:8080/jasperserver

	Usuario: jasperadmin
	contraseña: jasperadmin	

Si no tenemos acceso a entorno grafico y el puerto 8080(Por defecto) esta abierto, podemos acceder desde el navegador a la aplicacion::

	{urlcliente}:8080/jasperserver

jasperserver con webservice
---------------------------

Primero hay que configurar las conexiones con bases de datos.

#. Click derecho sobre Data Source, seleccionamos Add Resource > Data Source y configiramos la conexion.

#. Podemos utilizar o bien la carpeta reports o crear una carpeta para nuestro cliente y hay añadimos los report con click derecho y Add Resource > jasper report, tenemos que indicar el fichero con el report  y en la pestaña de data source seleccionamos una conexion.

#. Para acceder al report desde webservice utilizamos la url::

	{urlservidor}:{puerto}/jasperserver/rest_v2/reports/{carpetacliente}/{nombreport}.{extension(normalmente pdf)}

Podemos crear usuarios y configurar permisos.



