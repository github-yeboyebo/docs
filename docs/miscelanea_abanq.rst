Miscelánea AbanQ
================

.. toctree::
   :maxdepth: 2

Manejo de puertos COM (Puerto serie)
------------------------------------

#. Necesitamos la siguiente información relativa al puerto COM::

	Nombre Puerto
	Velocidad
	Control de Flujo
	Paridad
	Bit de Datos
	Bit de Parada
	Tiempo de Espera

#. Creamos una nueva instancia del objeto **FLSerialPort**::

	_i.printerF = new FLSerialPort(nombrePuerto);

#. Configuramos los datos anteriores y abrimos la nueva conexión con la función **open()**::

	_i.printerF.setBaudRate(velocidadPuerto);
	_i.printerF.setFlowControl(controlFlujoPuerto);
	_i.printerF.setParity(paridadPuerto);
	_i.printerF.setDataBits(bitPuerto);
	_i.printerF.setStopBits(bitParadaPuerto);
	_i.printerF.setTimeout(0, tiempoEsperaPuerto);

	if (!_i.printerF.open()) {
		delete _i.printerF;
		sys.warnMsgBox(sys.translate("No se ha establecido comunicación con el puerto"));
		return false;
	}

#. Según el hardware la cadena a enviar podría variar, pero el método idéntico. Se convierte la cadena hexadecimal a decimal y la enviamos a pedazos con **putch()**::

	for (var i = 0; i < cadena.length; i += 2) {
		_i.printerF.putch(parseInt(cadena.mid(i, 2), 16));
	}

#. Para la recogida de datos, procedemos de forma inversa. Recogemos el dato con la función **getch()** y lo convertimos en una cadena hexadecimal::

	dato = _i.toHex(_i.printerF.getch()).toString();

#. Cuando hayamos finalizado, cerramos la conexión con **close()**::

	_i.printerF.close();


Tanto las funciones que no aparecen en la documentación como ejemplos de código pueden encontrarse en el `siguiente enlace <http://151.80.174.89/svn/funcional/tronco/tpv_fiscal_chil/tronco/flfact_tpv.qs>`_, perteneciente a la funcionalidad *tpv_fiscal_chil*. Se recomienda partir de las siguientes funciones::
	
	establecerImpresoraFiscalChile
	imprimirDatosFiscalChile
	recibirDatosFiscalChile
	desconectarImpresoraFiscalChile

Levantar FCGI ElGanso
---------------------

El servidor se encuentra en la siguiente dirección::

	83.56.38.228

Debemos entrar con usuario infosial y ejecutar el script **Script_cron.sh** que se encuentra en el home.
Si deseamos reiniciar y levantar todos los sockets, entonces lanzaremos directamente::

	./Script_cron.sh

Sin embargo, si queremos hacerlo solamente para una tienda, lo lanzaremos seguido del código de tienda (en minúsculas). Por ejemplo, para la tienda de Carnaby lanzaríamos::

	./Script_cron.sh acar