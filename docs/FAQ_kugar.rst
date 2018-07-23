Kugar FAQ
====================================

.. toctree::
   :maxdepth: 2


Q.- El informe se bloquea.
------------------------------------

Si ocurriera este caso, lo más problable es que los niveles en nuestro fichero de archivos **.kut** los niveles no esten bien definidos. Siempre debe haber un nivel más en el codigo del informe que en la consulta asociada al informe.

CÓDIGO::

	<group>
		<level>0</level>
		<field>empresa.cifnif</field>
	</group>

Como vemos en este caso la consulta solo tendrá el nivel 0, por lo tanto el archivo del informe (**.kut**) deberá tener los niveles 0 y 1 para que no se bloquee.

CÓDIGO::

	<Detail Height="0" Level="0" /> 
 	<DetailHeader Height="85" Level="1" >
 		.....
 		.....
 		.....
 	</DetailHeader>
 	<Detail Height="20" Level="1" >
 		.....
 		.....
 		.....
 	</Detail>

Aquí vemos que el informe tiene correctamente dos niveles. Otra cosa necesaria para el correcto funcionamiento del informe es necesario que todos los niveles tengan la etiqueta **<Detail>**.


Q.- Solo se muestra el primer registro de una consulta.
----------------------------------------------------------

La solución a esto, pasa por comprobar que en la consulta todo lo que esté en las etiquetas de **<group>** debe aparecer también en el <select> de la siguiente manera.

CÓDIGO::

	<group>
		<level>0</level>
		<field>**empresa.cifnif**</field>
	</group>

	<select>
		a.referencia,
		a.descripcion,
		a.pvp,
		f.codfamilia,
		empresa.nombre,
		**empresa.cifnif**
	</select>



