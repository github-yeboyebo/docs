Despliegue aplicación web
=========================

Elementos necesarios
--------------------
* Actualizamos repositorios::
	
	sudo apt-get update
	si tenemos una version de ubuntu anterioro a 14.04 hay que 
	 sudo add-apt-repository ppa:fkrull/deadsnakes

	 sudo apt-get update

	 sudo apt-get install python3.4

	 alias python3 python3.4


* Python 3.4 o superior, pip y virtualenv::

	sudo apt-get install python3
	sudo apt-get install python3-setuptools
	sudo apt-get install python3-pip
	sudo pip install virtualenv

* Servidor web Nginx::

	sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib nginx
	sudo apt-get install python3.4-dev

* PostgreSql 9.X


Instalación
-----------

Nuestra aplicación web se ubicará en el directorio **/var/www/web/nombreproyecto** --*nombreproyecto* será el nombre del entorno/aplicación Django (podría haber varios)--

#. Creamos dentro tres directorios::

	logs: Dentro de este directorio se ubicarán los ficheros de log de django, gunicorn y yebo (mensajes provenientes de los debug que coloquemos en nuestra aplicación)

	src: Aquí desplegaremos la aplicación.

	run: Carpeta donde se alojará el socket que gestiona la conexión de Django.

#. Creamos un entorno virtual de python::

	  sudo virtualenv -p python3 nombreproyecto.

#. Damos permisos correspondientes al usuario y grupo nginx para que tenga acceso::

	 chown -R www-data:www-data  /var/www/web/XXXX

#. Establecemos permisos al directorio::

	 chmod  777 /var/www/web/XXXX

#. Lo activamos::

	 source bin/activate

#. Instalamos Gunicorn::

	 pip install django gunicorn psycopg2
	 sudo bin/pip3 install gunicorn -I   (para monitorización de procesos worker)
	 
#. Instalamos librerias de python necesarias::
	
		 pip install -r ruta a requirements.txt



Configuración gunicorn ubuntu 14
--------------------------------

**Gunicorn**

El fichero de configuración de Gunicorn se encuentra en **/etc/init/gunicorn.conf** 
Si queremos tener varios proyectos debemos crear un gunicorn_XXXX.conf::

	description "Aplicación Gunicorn para XXXXXX"

	start on runlevel [2345]
	stop on runlevel [!2345]

	respawn
	setuid www-data
	setgid www-data
	chdir /var/www/web/nombreproyecto/src

	exec ../bin/gunicorn --workers 3 --bind unix:../run/gunicorn.sock   [APLICDJANGO].wsgi
	##exec ../bin/gunicorn --workers 3 --bind 127.0.0.1:8000 YEBOYEBO.wsgi Si no vamos a utilizar socket podemos ponerlo así

**gunicorn_XXX** será un servicio. Para que empieze a funcionar debemos arrancarlo con *service gunicorn_XXX start*. Cada vez que hagamos un cambio en LEGACY o en configuraciones de nuestra aplicación Django debemos reiniciar el servicio.


Configuracion gunicorn ubuntu 16
--------------------------------

**Gunicorn**
El fichero de configuracion de gunicorn se encuentra en **/etc/systemd/system/gunicorn.service**
Si queremos tener varios proyectos debemos crear un gunicorn_XXXX.conf::

	[Unit]
	Description=gunicorn daemon
	After=network.target

	[Service]
	User=www-data
	Group=www-data
	WorkingDirectory=/var/www/django/elganso/AQNEXT/CLIENTES/ELGANSO/
	ExecStart=/var/www/django/elganso/bin/gunicorn --workers 3 --bind 127.0.0.1:24100 AQNEXT.wsgi:application

	[Install]
	WantedBy=multi-user.target


Una vez creado el fichero hay que habilitarlo para systemctl::

	systemctl enable gunicorn.service

Para arrancar el servicio::

	sudo systemctl start gunicorn.service 

	o

	sudo systemctl start gunicorn

Si cambiamos algo del fichero gunicorn.service hay que reiniciar el demonio::

	systemctl daemon-reload



Configuracion NGINX
-------------------

**Nginx**

Para **Nginx** creamos un fichero .conf donde definimos nuestro virtualhost para la aplicación en **/etc/nginx/sites-avaible/aplicacionXXXX.conf**::

	server {
		listen 80; ##puerto en el que escuchará
		client_max_body_size: 100M;
		server_name 84.124.11.214;  ##nombre del servidor o dominio
		location /LOC/static/ { ##ruta donde se encuentra los ficheros estáticos de aplicación
			alias   /var/www/web/proyectoenv/src/assets;
		}
		location /LOC {
			include proxy_params;
			proxy_pass http://unix:/var/www/web/proyectoenv/run/gunicorn.sock; ##ruta del socket de Gunicorn
			##proxy_pass http://localhost:8000/; Si no vamos a utilizar socket podemos ponerlo así
		}

	}

Una vez creado el archivo debemos hacer un enlace simbolico a sites-enables::

	ln -s /etc/nginx/sites-available/aplicacionXXX.conf /etc/nginx/sites-enabled/aplicacionXXX.conf

Antes de reiniciar el servicio, deberemos validar la configuración con *sudo nginx -t*. Si no hemos obtenido ningún error, podremos reiniciarlo con *sudo service nginx restart*.

**Django**

Para la configuración de *Django*, debemos editar el fichero **src/YEBOYEBO/local.py**::

	ALLOWED_HOSTS = (
		'localhost',
		'serverip',
	)

	LOGGING = {
		'version': 1,
		'disable_existing_loggers': False,
		'formatters': {
			'brief': {
				'format': '%(message)s',
			},
			'default': {
				'format': '%(asctime)s %(levelname)-8s %(name)-30s %(message)s',
				'datefmt': '%Y-%m-%d %H:%M:%S',
			}
		} ,
		'handlers': {
			'log_file_gunicorn': {
				'level':'INFO',
				'class': 'logging.handlers.RotatingFileHandler',
				'filename':  '../log/gunicornerror.log',
				'maxBytes': 1000,
				'formatter': 'default'
			},
			'log_file_django': {
				'level':'INFO',
				'class': 'logging.handlers.RotatingFileHandler',
				'filename':  '../log/django.log',
				'maxBytes': 1000,
				'formatter': 'default'
			},
			'log_file_YEBOYEBO': {
				'level':'DEBUG',
				'class': 'logging.handlers.RotatingFileHandler',
				'filename':  '../log/yebo.log',
				'maxBytes': 1000,
				'formatter': 'default'
			},
		},
		'loggers': {
			'gunicorn.error': {
				'level': 'INFO',
				'handlers': ['log_file_gunicorn'],
				'propagate': True,
			},
			'YEBOYEBO': {
				'handlers': ['log_file_YEBOYEBO'],
				'level':'DEBUG',
			},
			'django': {
				'handlers': ['log_file_django'],
				'level':'DEBUG',
			},
		}
	}

	#Configuración de conexión con bases de datos
	DATABASES = {
		'default': {
			'ENGINE': 'django.db.backends.postgresql_psycopg2',
			'NAME': 'YEBOYEBO',
			'USER': 'operador',
			'PASSWORD': 'operador',
			'HOST': 'localhost',
			'PORT':'5432'
		}
	}

	#Aquí debemos añadir nuestras aplicaciones
	YEBO_APPS=('YBWSEGUR','YBWELGANSO')

	INSTALLED_APPS = (
		'django.contrib.auth',
		'django.contrib.contenttypes',
		'django.contrib.sessions',
		'django.contrib.staticfiles',
		'rest_framework',
		'YBWCOMUN',
		'YBPORTAL'
	)+YEBO_APPS

	USE_X_FORWARDED_HOST = True
	#LOC es la localización que tengamos configurada en Nginx
	FORCE_SCRIPT_NAME='/LOC/'
	STATIC_URL = '/LOC/static/'
	TEMPLATE_CONTEXT_PROCESSORS=(
		'django.core.context_processors.request',
	)
	DEBUG=False


Errores comunes
---------------

Algunas versiones de virtualenv dan problemas, una solucion puede ser desinstalar(pip uninstall virtualenv) la version que se este utilizando(se puede comprobar con pip show virtualenv) e instalar una anterior con::

	easy_install "virtualenv<V.VV"

Puede haber un error con la codificacion de los ficheros .py::

  SyntaxError: Non-ASCII character '\xc3' in file ../../YBLEGACY/FLUtil.py on line 102, but no encoding declared;

Se soluciona añadiendo estas lineas al principio de cada fichero .py::
	
	#!/usr/bin/env python
	# -*- coding: utf-8 -*-

Tambien puede dar un error con locale::

	return _setlocale(category, locale)
	locale.Error: unsupported locale setting

Ejecutando el comando locale deberia salirnos algo asi::
	locale: Cannot set LC_CTYPE to default locale: No such file or directory
	locale: Cannot set LC_MESSAGES to default locale: No such file or directory
	locale: Cannot set LC_ALL to default locale: No such file or directory
	LANG=es_ES.UTF-8

Debemos seleccionar los locale validos con::

	export LC_ALL=es_ES.UTF-8
	export LC_CTYPE=es_ES.UTF-8

	export LC_ALL=es_ES.UTF-8
    export LC_CTYPE=es_ES.UTF-8
    export LC_TYPE=es_ES.UTF-8
    export LANG=es_ES.UTF-8
    export LANGUAGE=es_ES.UTF-8


Es posible que el error se deba a una mala configuracion en el usuario con el que logeamos a la maquina y que el error vuelva a aparecer, en ese caso debemos añadir las lineas anterior al fichero .bashrc que se encuentra en la home de el usuario.

Si la configuracion de el usuario da problemas debemos instalar como **sudo su**. Reconoceremos este error porque puede ser que te pida que te ejecutes las instrucciones sudo con sudo -H, o al instalar los requirements.txt en el entorno virtual no se instalen correctamente. 


Si gunicorn no se arranca correctamente, el 90% de las veces se suele deber a la carpeta de la aplicacion no tiene los persmisos bien configurados::

	chown -R www-data:www-data  /var/www/web/XXXX
	chmod  777 /var/www/web/XXXX


Desplegar en domino yeboyebo.es
-------------------------------

Conectar al gestor de nuestro dominio y en la seccion *Mis Pedidos* seleccionamos gestión del dominio *yeboyebo.es*. En la seccion de *Registros* añadimos nombre(El nombre del subdominio) y direccion IP del servidor donde este desplegada la aplicacion(Sin puerto).

Ya solo queda configurar NGINX para que acepte peticiones desde nuestro dominio web utilizando nombre.yeboyebo.es, ej:: 

	listen 8080;
	server_name atk.yeboyebo.es;

El puerto por el que escucha NGINX es el puerto que tenemos que ulizar para conectarnos a traves de nuestro dominio, por ejemplo este seria atk.yeboyebo.es:8080

CrossDomain
-----------

Si el clientes necesita realizar peticiones a la aplicacion desde un servicio web o desde otra maquina ajena al servidor es necesario permitir las peticiones entrantes, para ello configuramos nginx añadiendo las siguientes lineas::

		location / {
	        if ($request_method = 'POST') {
                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
	        }
	        include proxy_params;
	        proxy_pass http://localhost:24100/;
        }


La aplicacion REST esta restringida a peticiones PUT desde el mismo servidor, para peticiones externas vamos a utilizar el route **url=r'^REST/{prefix}/{lookup}/csr/(?P<accion>\w+)$'**, Ejemplo::

	http://89.29.153.34:9000/almacen/REST/articulos/10/csr/actualizar

El route **csr** esta preparado para aceptar peticiones POST y recoger los parametros enviandolos a la acciones correspondiente del modelo que indiquemos en prefix


HTTPS
-----

Si queremos desplegar una aplicacion de forma segura con HTTPS y SSL lo primero que hay que tener en cuenta es que la conexion segura se aplica al dominio sobre el que nos conectamos(ejemplo: yeboyebo.es) y se configura en el servidor web(NGINX).

Primero de todo necesitamos generar un certificado digital para conexion segura a nuestro dominio, podemos utilizar una entidad certificadora del grupo linux que genera certificados digitales de forma gratuita::

	https://certbot.eff.org/#ubuntuxenial-nginx

Aqui explica mejor como crear el certificado y configurar NGINX con los certificados generados::

	https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04

Configuracion NGINX que me ha funcionado::


	server {
        listen 9000;
        client_max_body_size 100M;
        #server_name 89.29.153.34;
        server_name dev.yeboyebo.es;
        rewrite ^ https://dev.yeboyebo.es$request_uri? permanent;
	}
	server{
        listen 443;
        ssl on;
        #server_name 89.29.153.34;
        server_name dev.yeboyebo.es;
        ssl_certificate        /etc/letsencrypt/live/dev.yeboyebo.es/fullchain.pem;
        ssl_certificate_key    /etc/letsencrypt/live/dev.yeboyebo.es/privkey.pem;

        location /static/ {
                alias   /var/www/django/dev/AQNEXT/clientes/desarrollo/assets/;
        }
        location / {
                if ($request_method = 'POST') {
 						add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                include proxy_params;
                proxy_pass http://localhost:24100/;
        }

	}


Para configurar django para enviar peticiones HTTPS he acudido a esta pagina::

	http://www.marinamele.com/2014/09/security-on-django-app-https-everywhere.html
