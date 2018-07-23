Saiku
================

.. toctree::
   :maxdepth: 2

Instalación
------------------------------------
#. Descargar `SAIKU <http://community.meteorite.bi/>`_::	
	 
	Pulsamos sobre la primera opción, (Download saiku CE).
	Descargará la última versión (este manual está realizado para la versión 3.5)

#. Descomprimir la carpeta en la ruta que queramos.

#. Iniciamos el proceso de saiku ejecutando::

	./start-saiku.sh

#. Accederemos a Saiku, si todo ha ido correctamente, nos aparecerá un diálogo para loguearnos::

	http://localhost:8080

#. Una vez logueados, entraremos en la interfaz.

Administración
------------------------------------
1. Crear usuarios:

	Los usuarios solo podrán crearlos los usuarios administradors que son los que tiene el roll de **ROLE_ADMIN**.
	 
	Para crear usuarios, accederemos a la pestaña **Admin Console** pulsando sobre el botón **A**. 

	En el apartado de **User Management** podemos ver los usuarios que hay creados y si pulsamos sobre **Add User** añadiremos un nuevo usuario. 

	Le pondremos el rol de **ROLE_USER** si no queremos que tenga acceso a la administración o **ROLE_ADMIN** y **ROLE_USER** si queremos que tenga acceso a todo.
	

2. Crear cubo:

	Para crear el cubo hay que configurar 2 apartados, por un lado la conexión con la base de datos de la que vamos a extraer la información y por otro lado el esquema.

	Accederemos a estos dos apartados desde **Data Source Management**.

	2.1. Data Sources
		
		Al pulsar en Data Source, nos aparecerá un formulario para informar los datos de conexión:

		**Name:** Nombre del Data Source, Ej. *elganso*

		**Conexion Type:** Tipo de conexión, utilizaremos *Mondrian*

		**URL:** Aquí informaremos donde está la bae de datos. Ej. *jdbc:postgresql://localhost:55432/comun_elganso_bi*

		**Schema:** De los esquemas que hay creados *(Punto 2)*, eligiremos uno.

		**Jdbc Driver:** Informaremos el driver correspondiente del origen de nuestros datos. En nuestro ejemplo: *org.postgresql.Driver*

		**Username:** Usuario

		**Password:** Constraseña 

		Si dentro de Data Source pulsamos sobre *Advanced*, aparecerá una caja de texto y podremos crear el el Data Source manualmente:

		Ej: 
		type=OLAP

		name=pruebas

		driver=mondrian.olap4j.MondrianOlap4jDriver

		location=jdbc:mondrian:Jdbc=jdbc:h2:../../data/earthquakes;MODE=MySQL;Catalog=mondrian:///datasources/pruebas.xml;JdbcDrivers=org.h2.Driver

		username=sa

		password=



	2.2. Schema
		
		En el apartado esquema, lo que haremos es elegir el esquema.xml que tendremos creado y subirlo.

		El esquema es un fichero xml en el cual indicamos las tablas de las cuales vamos a coger los datos, los atributos, las dimensiones, las jerarquías y los distintos cubos que van a contener nuestro modelo.

		Puedes encontrar el schema para el saiku de El Ganso `aquí <https://drive.google.com/drive/folders/0BxogFbb65QgfM3oyWF9kWEl4N2M?usp=sharing>`_ 

 
	`Más información sobre creación esquemas <http://mondrian.pentaho.com/schema.html#What_is_a_schema>`_ 


Consultas
------------------------------------
Se pueden realizar consultas sobre los distintos cubos que hay creados.

Las consultas las puede realizar cualquier usuario (rol ADMIN o rol USER).

Para realizar consultas pulsaremos sobre el icono de la carpeta con el signo **+** o pulsaremos sobre nueva pestaña.

Una vez que hemos accedido a crear una nueva consulta, nos aparecerá un formulario donde seleccionaremos el cubo que queramos utilizar, una vez elegido nos aparecerán las medidas **Measures** y las dimensiones **Dimensions** que tiene dicho cubo definidas en su esquema.
 
Para poder mostrar datos, elegiremos como mínimo, la/s medida/s que queremos mostrar en **Measures** (se mostrara en columna) y la/s fila/s en el apartado de **dimensiones**. 

Podemos agregar más columnas (aparte de las seleccionadas en measures) seleccionando distintas Dimensions.

En el apartado de filtros podemos añadir dimensiones para filtrar solo los datos que nos interesen.

Una vez que tengamos las consulta creada, podemos utilizar los distintos botones para imprimirla, exportarla a pdf, excel, gráficos etc.

Para guardar la consulta y volver a utilizarla en un futuro pulsaremos sobre el icono del disquete y nos aparecerá un árbol de carpetas donde poder guardarla.

Para recuperar una consulta guardada anteriormente, pulsaremos sobre el icono de la carpeta y accederemos a un árbol de carpetas donde seleccionaremos la consulta guardada, pudiendo lanzarla, editarla....

OJO!
------------------------------------
Si no informas la propiedad 'allMemberName' de una jerarquía estarás obligado a meter una dimensión de esa jerarquía para obtener datos, en caso contrario, obtendrás los datos de el primer registro (o ninguno).