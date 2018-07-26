AQNEXT
=======


Primeros pasos
--------------

#. Comprobamos que nuestro ordenador tiene minimo python3.5 instalado::

    python3 --version (deberia devolver mínimo Python 3.5)

#. Si no tenemos python3.5 instalamos::

    sudo apt-get install python3.5

#. Empezamos con las instalaciones::

    alias python=python3
    sudo apt-get install python3-setuptools
    sudo apt-get install python3-pip
    sudo apt-get install python-pip python-dev libpq-dev postgresql-contrib nginx
    sudo pip install virtualenv
    sudo apt-get install python3-dev
    sudo apt-get install build-essential
    sudo apt-get install nodejs
    sudo apt-get install npm
    sudo apt-get install xdotool
    sudo apt-get install wmctrl
    sudo apt-get install nodejs-legacy
    sudo npm install bower@1.8.4 -g
    sudo npm install webpack@1.13.3 -g
    sudo npm install eslint@3.11.1 -g

#. Para evitar instalar demasiadas librerías de forma global crearemos un entorno virtual, creamos una carpeta llamada django en nuestro ordenador(/var/www/dev/django)::
    
    mkdir django
    sudo virtualenv -p python3 django
    chmod -R 777 django
    cd django
    source bin/activate
    pip3 install django gunicorn psycopg2

#. Copiar AQNEXT(Ver :ref:`estructura-de-aplicacion`) la aplicacion en /var/www/dev/django/aqnext

#. Una vez tengamos la carpeta aqnext dentro de django podemos instalar en nuestro entorno virtual las librerías necesarias, estando en el raiz de django, activamos el entorno virtual si no lo tenemos ya(source bin/activate) y ejecutamos::
    
    pip install -r aqnext/motor/requirements.txt

#. La aplicación no incluye las librerias externas(jquery,react,etc..) para poder instalarlas todas cómodamente en la raiz(AQNEXT) debería haber un fichero **package.json** y otro **requirements.txt**, con ellos podemos instalar todas las librerías necesarias para el funcionamiento de la aplicación, pero es mucho mas fácil descargar el siguiente fichero de drive y descomprimirlo en django/aqnext/motor/YBCORE::

    https://drive.google.com/open?id=1HNcbiyqD1Y_xDHsVmBs4y8ufWeVcwtI8


.. _ficheros-de-arranque:

Uso de ficheros SVN y arranque
-------------------------------

En aqnext/control tenemos el fichero **runserver.sh** recibe dos parámetros::

    1 .- El nombre de un cliente.
    2 .- El puerto de escucha.

Si existe el cliente, abre 3 pestañas de terminal::

    1 .- WebpackCss con watchmode.
    2 .- WebpackJs con watchmode.
    3 .- Arranca el servidor en el puerto dado.

Si no se le pasa cliente o no existe, sale de la aplicación.
Si no se le pasa puerto, coge por defecto el 8000.

.. _estructura-de-aplicacion:

Estructura de la aplicacion
---------------------------

En el raíz de la aplicación tenemos los siguientes directorios:

* clientes: Aqui se crean los nuevos clientes, para crear un nuevo cliente basta con hacer una copia de template y crear un nuevo fichero local.py

* control: Herramientas para ayudar al desarrollo de aplicaciones.

* mantver: Herramientas para ayudar a la traduccion de scripts/tablas.


Dentro de la aplicación de cada cliente tenemos los siguientes directorios:

* AQNEXT: Configuración de django, el fichero mas interesante aquí es local.py donde indicaremos la base de datos y el cliente con que vamos a trabajar.

* apps: Aqui se alojan todos los templates de las areas de la aplicación.

* legacy: Scripts traducidos.

* config: Ficheros de configuracion del cliente, aqui se registran que tablas y scripts se van a traducir, asi como la información para traduccion de modelos y scripts::

    * dependencias.json: Debemos informar **fun_aq** con el nombre de nuestro cliente.

    * registros.json: JSON con los scripts que se van a utilizar en la aplicación web.

    * urls.json: JSON con los modelos/tablas que se van a utilizar en la aplicación web. También se utiliza para registrar las aplicaciones virtuales.

* logs: Tres ficheros de logs::

    * django.log: Log solo para desarrollo, en el se pueden ver todas las consultas a BBDD que se realizan en la aplicación.

    * yebo.log: Log de aplicación, en el se capturan los errores de la aplicación, también podemos escribir en el con qsatype.debug("*")

    * django.log: Log para despliegue.

* models: Aquí se registran los scripts propios solo de la parte web de la aplicación.


.. _formularios-aqnext:

Formularios AQNEXT
------------------

Existen tres tipos de formularios por defecto que podemos definir para una tabla del modelo, aparte podemos también crear formularios personalizados.
Los formularios se ubicaran dentro del directorio del cliente en el módulo que le corresponda, por ejemplo los formularios de *facturascli* para *CLIENTE* se ubicaran en **CLIENTES/CLIENTE/facturacion/templates/facturacion/plantillas/#formularios#.html**.

* *Formulario maestro:* Su nombre vendrá precedido por master, ejemplo *masterfacturascli.html*

* *Formulario de edición:* Se llamara igual que la tabla, por ejemplo *facturascli.html*

* *Formulario de creación:* Formulario para crear nuevo registro sobre una tabla su nombre vendrá precedido por newrecord, ejemplo *newrecordfacturascli.hml*

* *Formularios personalizados:* Si para algún caso especial necesitamos un formulario que no sea ninguno de los tres anteriores, podemos crear un fichero *###.html* donde ### puede ser cualquier nombre.


Todos los formularios se invocaran a partir de su *url*, normalmente solo tendremos que añadir la url del maestro al menú y a partir de ahí la aplicación ya se ira direccionando donde se pida.

* *Formulario maestro:* http://urlservidor:puerto/facturacion/facturascli/master

* *Formulario de edición:* http://urlservidor:puerto/facturacion/facturascli/2103

* *Formulario de creación:* http://urlservidor:puerto/facturacion/facturascli/newrecord

* *Formulario de edicion personalizado:* http://urlservidor:puerto/facturacion/facturascli/2103/personalizado

* *Formulario maestro personalizado:* http://urlservidor:puerto/facturacion/facturascli/custom/personalizado


Creación de Formularios AQNEXT
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Los formularios tienen formato JSON. Todos los formularios de AQNEXT sean del tipo que sean tienen la siguiente estructura básica::

    {%comment%}
        (Opcional)
    {%endcomment%}

    "querystring":{
       Filtro inicial sobre tabla
    },
    "schema":{
       Tablas auxiliares
    },
    "layout":{
        Aquí van los componentes
    },
    "acciones":{
        Definición de acciones de servidor
    }

* **Comment:** Etiqueta de django que se utiliza para colocar comentarios o notas sobre el formulario.

* **querystring:** Filtros en formato django que se aplicarán sobre la tabla (Ver :ref:`querystring-aqnext`), ejemplo::

    Con el siguiente filtro vamos a indicar que queremos un límite de 50 elementos
    forzando la paginación, los elementos los queremos ordenados por código
    y solo aquellos cuyo campo pda sea igual a En PDA.

    "querystring":{"p_l": 50, "p_c": true, "o_1": "codigo","s_pda__exact":"En PDA"}

* **schema:** Cuando abrimos el formulario de una tabla, por ejemplo albaranescli, hacemos una consulta al servidor para que nos devuelva los albaranes filtrados por querystring, con **schema** podemos añadir otras tablas a la consulta, si estas tablas están relacionadas con el padre indicamos mediante *rel* el campo porque el que están relacionadas, además podemos también filtrar con *querystring*, por ejemplo si estamos con un formulario de albaranescli y queremos además sus lineas::

    "schema":{
        "lineasalbaranescli":{"rel":"idalbaran","querystring":{"p_l":50,"p_c":1}}
    }

* **layout:** Donde se indicarán los componentes (Ver :ref:`componentes-aqnext`) que formarán nuestro formulario.

* **acciones:** Acciones que se invocaran desde los eventos del formulario, se pueden invocar acciones desde diferentes componentes(botones, iconos, tablas, etc..).(Ver :ref:`acciones-aqnext`)

.. _componentes-aqnext:

Componentes AQNEXT
------------------

Estos son los diferentes componentes que podemos incluir en nuestros formularios.

.. _materialicons: https://material.io/icons/   

Los iconos se encuentran en materialicons_, de ahí copiamos el nombre del icono que busquemos y simplemente lo pegamos donde indique icon.

* **Formularios:** :ref:`yb_form`

* **Tablas/Grid:** :ref:`yb_grid`

* **fdb, Field:** :ref:`yb_fielddb`

* **Botones:** :ref:`yb_button`

* **newrecord:** El boton circular que aparece abajo a la izquierda en los maestros para añadir un nuevo registro a la tabla::

    "newRecordFacturascli":{
        "componente":"newrecord",
        "class":"info",
        "icon":"add"
    }   

* **Groupbox:** :ref:`yb_groupbox`

.. _yb_form:

Forms
~~~~~

Formularios de creación(create) o edición(update)::

    "albaranescliForm":{
        "prefix":"albaranescli",
        "componente":"YBForm",
        "class":"claseCSS",
        "submit":"create",
        "success": [{"slot": "redirect"}],
        "fields":{
            "gb__grupo1":{
                "title": "",
                "fields":{
                    "idalbaran": {"disabled":true},
                    "codcliente":{
                                   "desc": "nombre",
                                   "label": "Cliente"
                                  },
                    "cifnif":{"className":"fielddb"},
                    "direccion":{},
                    "codigo":{},
                    "nombrecliente":{}
                }
            }   
        },
        "exclude":{}   
     }

**Obligatorios:**

* **prefix:** nombre tabla en BD.
* **componente:** YBForm
* **submit:** create, update
* **success:** redirect, return

**Opcionales:**

* **className:** Clases CSS para aplicar estilos personalizados.
* **fields:** Campos que componen el formulario, pueden estar separados en Grupos("gb_nombregrupo"):
    #. *disabled*
    #. *desc*: Indicamos campo para buscar cuando se trata de campos relacionados que no esten indicados como ForeignField.
    #. *label*
    #. *className*

.. _yb_grid:

Grid
~~~~

 Tabla cuyas columnas pueden ser campos del modelo o acciones::

    "masterAlbaranescli": {
        "componente": "YBGrid",
        "label": "Albaranes de venta",
        "prefix": "albaranescli",
        "filter": {
                "codigo": null,
                "fecha": {
                    "filterType": "desde-hasta"
                },
                "codcliente": {"rel": false, "label": "Código de cliente"}
            },
        "colorRowField": "rowColor",
        "columns": [
            {"tipo": "field", "listpos": "title", "key": "codigo"},
            {"tipo": "foreignfield", "listpos": "body", "key": "proyecto", "label":"Proyecto", "flex": 2}
            {"tipo": "field", "listpos": "body", "key": "fecha"},
            {"tipo": "field", "listpos": "subtitle", "key": "nombrecliente"},
            {"tipo": "field", "listpos": "secondaryitem", "key": "total"},
            {
                "tipo": "act",
                "key": "delete",
                "label": "Borrar Linea",
                "success": [
                    {"slot": "refrescar"}
                ]
            }
        ],
        "rowclick": "link"
    }

**Obligatorios:**

* **componente:** YBTable(Formato tabla), YBList(Formato Lista), YBGrid(Formato cambia segun tamaño de la pantalla)
* **prefix:** nombre tabla en BD.
* **filter:** Puede ser *buscador* o indicar campos a filtrar.
* **rowclick:** link, nombreAccion
* **columns:**
    #. *tipo*: field, foreignfield(Campos calculados), act
    #. *key*

**Opcionales:**

* **className:** Clases CSS para aplicar estilos personalizados.
* **columns:**
    #. *listpost*: Posición del campo en formato lista(title, body, subtitle, secondaryitem)
    #. *label*
    #. *width*
    #. *flex*: Permite ajustar el tamaño de forma proporcional a la pantalla.
    #. *success* Solo para acciones.

.. _yb_groupbox:

GroupBox
~~~~~~~~

Podemos agrupar diferentes elementos del formulario dentro de un groupbox, a estos elementos podemos aplicarles una claseCSS propia::

    "layoutprueba":{
        "componente":"groupbox",
        "className":"claseCSS",
        "style": {
            "paddingRight": "10px"
        },
        "layout":{

        }
    },

**Opcionales:**

* **className:** Clases CSS para aplicar estilos personalizados.
* **style:** Objeto JSON con estilos CSS para el groupbox

.. _yb_fielddb:

Field
~~~~~

Pueden ser field sencillos o campos relacionados de los que se extrae pk + descripcion::

        "fdb_codBarras": {
            "componente": "YBFieldDB",
            "prefix": "otros",
            "key": "codbarras",
            "desc": "descripcion",
            "label": "Artículo",
            "tipo": "55",
            "rel": "articulos",
            "className": "",

            "actions": [{
                "signal": "enterPressed",
                "receiver": "field_cantidad",
                "key": "selectCantidad",
                "success": [{"slot":"refrescar"}]
            }]
        },
        "field_cantidad": {
            "componente": "YBFieldDB",
            "prefix": "otros",
            "className": " fielddb",
            "key": "field_cantidad",
            "label": "cantidad",
            "defaultvalue": 1,
            "tipo": 16,
            "actions": [{
                "signal": "enterPressed",
                "key": "actNuevaLineaPedidoCli",
                "success": [
                    {"slot": "refrescar"}
                ]
            }]
        }

* **componente:** YBFIELDB
* **prefix:** otros(para campos que no apuntan a ninguna tabla) o nombre tabla en BD.
* **key:** Nombre del campo, en relacionados el campo que vamos a guardar de la tabla.
* **desc:** Solo relacionados, nombre del campo por el que vamos a buscar
* **rel:** Solo relacionados, nombre de la tabla a buscar
* **label:** Etiqueta del campo que se mostrará en el navegador
* **tipo:**
    #. *55*: Campo relacionado con buscador
    #. *5*: Campo relacionado con selección
    #. *3*: String
    #. *6*: Text Area
    #. *16*: Number
    #. *26*: Fecha
    #. *27*: Hora

**Opcionales:**

* **className:** Clases CSS para aplicar estilos personalizados.
* **defaultvalue:**: Valor inicial
* **function:**: Función de servidor a la que llamara para hacer la consulta.
* **actions:**
    #. *signal*: enterPressed
    #. *key*: Nombre acción a ejecutar
    #. *receiver*: En caso de ser una acción de tipo focus o select, el receptor.
    #. *success* Ver success

.. _yb_button:

Button
~~~~~~

Botón que ejecuta acciones::

        "botonAccion": {
            "componente": "YBButton",
            "prefix": "pedidoscli",
            "label": "ENVIAR",
            "buttonType": "raised",
            "primary": false,
            "secondary": true,
            "action": {
                "key": "actNuevaLineaPedidoCli",
                "success": [
                    {"slot": "refrescar"},
                    {"receiver": "fdb_codBarras", "slot": "select"}
                ]
            }
        }

* **componente:** YBButton
* **prefix:** Nombre tabla en BD.
* **label:** Etiqueta del boton


Label
~~~~~


.. _menu-aqnext:

Menus
-----

Los menús se definen con forma de JSON, existe un menú general en **CLIENTES/#####/portal/templates/portal/menu_portal.json**, el menu general es el primero que se muestra en la aplicación, ejemplo::

    {
        "items": [
            {
                "NAME": "telsac",
                "TEXT": "ALBARANES DE SALIDA",
                "URL": "gestion/telsac/master",
                "ICON": "content_paste",
                "COLOR": "rgb(7, 180, 7)"
            },
            {
                "NAME": "envioequipos",
                "TEXT": "Envío de Equipos",
                "URL": "gestion/telsac/custom/envioequipos",
                "ICON": "send",
                "COLOR": "rgb(7, 180, 7)"
            },
            {
                "NAME": "recepcionequipos",
                "TEXT": "Recepción de Equipos",
                "URL": "gestion/telsac/custom/recepcionequipos",
                "ICON": "reply",
                "COLOR": "rgb(7, 180, 7)"
            },
            {
                "NAME": "telotc",
                "TEXT": "OT DE MANTENIMIENTO",
                "URL": "gestion/telotc/master",
                "ICON": "widgets",
                "COLOR": "rgb(7, 180, 7)"
            },
            {
                "NAME": "telotci",
                "TEXT": "OT DE INSTALACIÓN",
                "URL": "gestion/telotci/master",
                "ICON": "widgets",
                "COLOR": "rgb(7, 180, 7)"
            }
        ]
    }

Donde:

#. **NAME:** Aqui debemos escribir el nombre de la tabla o del template.
#. **TEXT:** Texto que aparecera en el dashboard.
#. **URL:** Ruta relativa a pantalla.
#. **ICON:** Ver Iconos
#. **COLOR:** Color del icono en dashboard.


Aplicaciones virtuales de cliente
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dentro de la carpeta app de cada cliente creamos las aplicaciones virtuales, las aplicaciones virtuales tienen acceso a todas las tablas y scripts de la aplicación, son una forma de poder tener una estructura de menus y navegación personalizada.

Estructura de una aplicación virtual
------------------------------------

* templates: Aqui se almacenan, dentro de la carpeta plantillas, los templates, incluye el menu de la aplicacion virtual, siempre con el nombre menu_nombreaplicacion. Pueden almacenarse paginas HTML estaticas. 

* viewset: Incluye el fichero views_nombreaplicacion, el cual permite realizar acciones sobre la aplicación virtual, tales como sobrecargar el login o cargar paginas estaticas.

.. _querystring-aqnext:

Querystring
-----------

Querystring permite hacer consultas a BBDD, tiene los siguientes modificadores:
   
    * **s_** , **q_**: El equivalente a un select, **s_** sería el equivalente a consultas con *AND* y **q_** a consultas con *OR*, esta formado por *s_campo__condicion:filtro*.

        * *Campo* es el campo del modelo o si se trata de un campo relacionado podemos utilizar "s_campo__campo2__condicion:filtro" ejemplo::

            s_referencia__pvp__gt:20

        * *Condición* puede ser:

            #. exact: busca valor exacto.

            #. iexact: busca valor exacto incluyendo mayúsculas y minúsculas.

            #. lt,gt: menor/mayor que filtro.

            #. lte,gte: menor/mayor o igual que filtro.

            #. startswith,endswith: busca cadenas que empiecen o terminen por el valor de filtro.

            #. in

            #. ne: not equal.

    * **f_**: (Ver :ref:`filtrosserver-aqnext`)

    * **p_l**: El equivalente a limit, se utiliza para la paginación por defecto el limite esta en 100.

    * **p_o**: offset, junto con *p_l* se utiliza para la paginación.

    * **p_c**: Fuerza la consulta para que devuelva el COUNT.

    * **fs_campo**: limita el numero de elementos que devuelve una consulta, solo devolvera los campos indicados con *fs_*.

    * **fh_campo**: Marca el campo como visible=false;

    * **a_BULK**: Para acciones sobre varios elementos(revisar).

.. _acciones-aqnext:

Acciones
--------

Se pueden invocar acciones desde diferentes eventos: botones, formularios, success(evento que se dispara al terminar correctamente una función), tablas, etc...


YBFielddb
~~~~~~~~~

Diferentes acciones que podemos invocar desde un campo de texto::

        "actions": [{
            "signal": "enterPressed",
            "key": "actNuevaLineaPedidoCli",
            "success": [
                {"slot": "refrescar"}
            ]
        }]

**Opcional:**

* **receiver:** Cuando tenemos una acción de tipo select/focus debemos indicar el nombre en layaout del receptor.


YBGrid
~~~~~~

* **deleterow:** Acción que se invoca solo desde grid, ejemplo::

    "delete":{
        "label" : "Borrar",
        "action":"deleteRow"
    }

General
~~~~~~~


* **legacy:** Ejecuta funciones de servidor, ejemplo::

    "actNuevaLineaAlbaranescli":{
        "label" : "Lineas albaran",
        "action":"legacy",
        "serverAction":"nuevaLinea",
        "params":{
            "codbarras":{
                "tipo":3,
                "verbose_name":"codbarras",
                "campo":"codbarras",
                "key":"codbarras",
                "validaciones":null
            },
            "cantidad":{
                "tipo":3,
                "verbose_name":"cantidad",
                "campo":"cantidad",
                "key":"cantidad",
                "validaciones":null
            }
        }
    }


BufferCommited
--------------

Acción que se ejecutara al completar todo el proceso de commit de un formulario, se registra en la parte web de la aplicación(models).



Campos Relacionados
-------------------

Podemos añadir campos relacionados directamente al modelo de nuestras apps(almacen,facturacion, o aplicaciones virtuales), para ello añadimos la funcion **getForeingFields**::

	def getForeingFields(self, model, template):
        #template indica quien llama al campo calculado(formRecord, master, template)
		#verbose_name y func no pueden tener el mismo nombre
		return [{'verbose_name':'nombre del campo calculado','func':'nombre de la funcion que devuelve campo calculado'},
		{'verbose_name':'calculado','func':',my_calculatefield'}]

	def my_calculatefield(self, model):
		#AQUI se pueden hacer las operaciones que necesitemos para retornar el campo.
        #model incluye los datos del campo
		return 'calculate'

Colores
-------

* cPrimary: #5744DE

* cSuccess: #449D44

* cInfo: #31B0D5

* cDanger: #D32F2F

* cWarning: #EC971F

* cLink: #4478DE


.. _filtrosserver-aqnext:

Filtros de servidor
-------------------

Podemos añadir a querystring el filtro **"f_":"name"** el cual sirve para los casos en que necesitemos un campo dinámico de servidor para filtrar(Por ejemplo el usuario logeado o el ejercicio del usuario), para poder utilizar estos filtros tenemos que añadir la función **getFilters**, la función retorna un array de JSON con los datos de los filtros que queremos aplicar::

	def getFilters(self, model, name, template=None):

		if name == 'almacenUsuario':
			return [{'criterio':'codalmacen__iexact','valor':'BNP'}]

		return []

**name** hace referencia al nombre que le demos en el template al filtro Ej.::

	"querystring":{"p_l":50,"p_c":true,"o_1":"codinventario","f_":"almacenUsuario"},
	"layout":{........


Validaciones iniciales
----------------------

Podemos aplicar ciertas restricciones a un template antes de invocarlo, por ejemplo que tenga un ejercicio almacenado, debemos indicar en el template que vamos a aplicar esas restricciones y donde navegará en caso de que fallen::

	"initValidation":{"error":{"aplic":"almacen","prefix":"vb_almacenesusu","template":"almacenusu","msg":"Debes seleccionar un almacen local"}},
	"querystring":{},
	"layot":{.......

Tambien tenemos que añadir la funcion **initValidation**, la funcion retornara True o False en funcion de si se cumplen las condiciones, **name** hace referencia al nombre del template desde el que invocamos::

	def initValidation(self, name, data):
		response = True

		if name == 'inventariosAlmacen':
			util = qsatype.FLUtil()
			try:
				settingKey = ustr( u"almacenUsr_" , qsatype.FLUtil.nameUser() )
				almacenUsr = util.readDBSettingEntry(settingKey)
				if not almacenUsr:
					response = False
			except Exception as e:
				response = False
			return response

		return response

.. _herramientas-migracion:

Herramientas de migración
-------------------------

Disponemos de las herramientas para la migración automática de modelos y scrips(**aqnext/mantver**).
Este traductor toma los ficheros a traducir de los ficheros urls.json y registros.json.
Para que funcionen correctamente las herramientas de traducción tendremos que activar el entorno virtual::

    source ../../bin/activate

Para la traducción de scripts/modelos podemos usar el fichero **devmantver.py**, el cual recibe tres parametros, dos de ellos opcionales:

    * Nombre de cliente(Obligatorio): -c nombrecliente

    * Traduccion solo scripts(Opcional): -s

    * Traduccion solo modelos(Opcional): -m 


Con el siguiente ejemplo se traducen todos los scripts/tablas del cliente tutorial, Ejemplo::
    
    python3 devmantver.py -c tutorial


Otra herramienta util para la migracion de tablas es **generaModels.py**, el cual genera los ficheros necesarios para añadir funcionalidad web, con las funciones y estructura basica, el cual recibe un solo parametro:

    * Nombre de cliente(Obligatorio): -c nombrecliente

Con el siguiente ejemplo se crearan todos los ficheros necesarios para trabajar con las tablas del cliente tutorial, Ejemplo::
    
    python3 generaModels.py -c tutorial



Existen una serie de Tags especiales para el preproceso que permiten impedir que el código pase a PYTHON::

        \\___NOPYTHON[[
        .....................................

         \\]]___NOPYTHON

Existe también un condicional para QSA con el que podemos indicar código que solo se ejecutara en django::

    if (sys.interactiveGUI() == "Django")

Para poder utilizar el código legacy en una aplicación se seguirán los siguientes pasos:



Parametros de accion
--------------------

Ejemplo, filtro(en filtro indicamos campos de otros por lo que filtrar) con campo de tabla padre(tipo rel toma valor de campo en otros) y campo codubicacion relacionado pudiendo ser nulo(blank=true)::

	"params":{
			"codubicacion":{
				"rel":"vb_ubicaciones",
				"aplic":"almacen",
				"key":"codubicacion",
				"desc":"codubicacion",
				"campo":"ubicacion",
				"verbose_name":"ubicacion",
				"tipo":55,
				"showpk":false,
				"filtro":["codalmacen"],
				"className":"modalRelated",
				"validaciones":null,
				"blank":true
			},
			"inventarios_codalmacen":{
				"tipo":"rel",
				"rel":"inventarios",
				"campo":"codalmacen"
			}
		}

