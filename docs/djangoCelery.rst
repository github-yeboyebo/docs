CELERY
======


Primeros pasos
--------------

Instalar celery, django-celery y rabbitmq-server::

    pip install celery
    pip install django-celery
    sudo apt-get install rabbitmq-server

El servidor RabbitMQ se localiza en **/usr/local/sbin** es posible que tengamos que configurar el archivo .bash_profile para añadirlo al path si no se inicia autocamicamente::

    export PATH=$PATH:/usr/local/sbin

Podemos comprobar el estado del servidor RabbitMQ con el comando::

    sudo rabbitmq-server -detached

Para detener el servicio de RabbitMQ:

    sudo rabbitmqctl stop

Una vez instalado RabbitMQ debemos crear los usuarios y el host virtual para ser usado por una aplicacion::

    sudo rabbitmq-server -detached
    sudo rabbitmqctl add_user myuser mypassword
    sudo rabbitmqctl add_vhost myvhost
    sudo rabbitmqctl set_permissions -p myvhost myuser ".*" ".*" ".*"

Y en la aplicación donde vayamos a usar celery añadimos la siguiente linea a local.py::

    BROKER_URL = "amqp://myuser:mypassword@localhost:5672/myvhost"

Configuración Django
--------------------

Tenemos que agregar ‘djcelery’ a INSTALLED_APPS::
     
    INSTALLED_APPS = [
     
        'djcelery',
    ]

Tambien podemos indicar algunos parametros de configuracion de celery::

    CELERY_RESULT_BACKEND = 'djcelery.backends.database:DatabaseBackend'
    CELERYBEAT_SCHEDULER = 'djcelery.schedulers.DatabaseScheduler'


Una vez instaladas vamos a generar y correr las migraciones::

    python manage.py syncdb
    python manage.py makemigrations
    python manage.py migrate


Configurar Celery
-----------------

Para configurar nuestro proyecto Django junto a Celery tenemos que crear un archivo llamado **celery.py** dentro de la carpeta AQNEXT del cliente::

    from __future__ import absolute_import

    import os

    from celery import Celery
    from django.conf import settings

    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'AQNEXT.settings')
    app = Celery('desarrollo', broker="amqp://desarrollo:desarrollo@localhost:5672/desarrollo")
    app.config_from_object('django.conf:settings')
    app.conf.update(
        task_serializer='json',
        accept_content=['json'],  # Ignore other content
        result_serializer='json',
        timezone='Europe/Madrid',
        enable_utc=True,
    )
    # This linea le dice a celery donde debe buscar los tasks.py que se encuentran en las carpetas de las app
    app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)


Tambien debemos añadir al fichero **__init__.py** que este al mismo nivel que **celery.py** lo siguiente::

    from __future__ import absolute_import
    from celery import Celery
    from .celery import app as celery_app



Dentro de la aplicacion del cliente creamos en el raiz de la aplicacion un archivo llamado **task.py**::

    from celery.task import periodic_task, task
    from celery.schedules import crontab
    from AQNEXT.celery import app

    @app.task
    def prueba_suma(x, y):
        return x + y
         
    @app.task
    def prueba():
        return "prueba"


Para ejecutar una funcion con celery podemos hacerlo importanto la funcion del archivo task y ejecutandola con **delay**::

    from facturacion import task


    class facturascli(LEGMODELS.facturascli,helpers.MixinConAcciones):
        pass
        class Meta:
            proxy=True

        @helpers.decoradores.accion()
        def prueba(self):
            resultado = prueba_resta.delay(self.pk, 9)
            return True


Si hacemos algun cambio sobre tareas de celery o añadimos alguna tenemos que reiniciar el demonio de rabbitmq::

    celery -A AQNEXT worker --loglevel=INFO --concurrency=10 -n worker1@%h -B -Ofair
    sudo service rabbitmq-server restart

INSTALAR ASGI RABBIT

pip install -U asgi_rabbitmq

**NOTA

Comprobar que todas las aplicaciones tengan un fichero __init__.py