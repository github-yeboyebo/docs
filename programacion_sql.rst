Programación SQL
====================================

.. toctree::
   :maxdepth: 2
	
	
	
Búsqueda de un array dentro de otro
------------------------------------
Con la función **string_to_array(campo,separador)** transformamos una cadena separada por 'separador' array.

Con el operador **@>** indicamos "contiene".

Con el la funcion **ARRAY[string]** transformamos un string en array.



Ejemplo:
Tenemos una tabla en la cual tenemos la columna subfamilia y la columna orden. En la columna codsubfamilia guardamos las subfamilias separadas por ","

+---------------+---------+
| codsubfamilia | orden   |
+===============+=========+
| 1,2,3,4,54,4  |    1    |
+---------------+---------+
| 303,4,5,8,87  |    2    |
+---------------+---------+

 

Para hacer directamente en un select cuantos registros contienen a la subfamilia 3 haríamos::

	var numRegSubfam = AQUtil.sqlSelect("em_ordenlineasordenest", "count(*)","string_to_array(codsubfamilia, ',') @> ARRAY['3']");


`Más información <http://www.postgresql.org/docs/9.1/static/functions-array.html>`_
	
Consultar tamaño de las tablas de una BD
-----------------------------------------
En la relación *information_schema.tables* podemos encontrar todas las tablas creadas por AbanQ. Para ello utilizamos el filtro *WHERE table_schema = 'public'*.

Aplicando la función **pg_relation_size(nombreTabla)** y a esta misma la función **pg_size_pretty(tamañoEnBytes)** obtendremos el tamaño de la tabla en bytes y en formato legible respectivamente. (Se consulta en bytes para poder ordernar por este dato).

La consulta sería la siguiente::

	SELECT
		table_name AS tabla,
		pg_size_pretty(pg_relation_size(table_name)) AS tamanio,
		pg_relation_size(table_name) AS tamanioBytes
	FROM
		information_schema.tables
	WHERE
		table_schema = 'public'
	ORDER BY
		pg_relation_size(table_name) DESC;

El resultado quedaría así:

+---------------------+-----------+----------------+
|  tabla              |  tamanio  |  tamaniobytes  |
+=====================+===========+================+
|  movistock          |  1167 MB  |  1223868416    |
+---------------------+-----------+----------------+
|  tpv_comandas       |  1062 MB  |  1113464832    |
+---------------------+-----------+----------------+
|  tpv_lineascomanda  |  946 MB   |  991657984     |
+---------------------+-----------+----------------+
|  stocks             |  777 MB   |  814456832     |
+---------------------+-----------+----------------+

	
Exportar consulta a CSV
-----------------------

El comando se lanza desde la consola (fuera de una BD) y sería el siguiente::

	psql _nombreBD_ -c "COPY (_consultaSql_) TO STDOUT WITH CSV HEADER " > _nombreFichero_.csv

Solamente habría que sustituir _nombreBD_, _consultaSql_ y  _nombreFichero_ por los datos deseados. 

	
Listar las tablas y sus PK
--------------------------

La consulta es la siguiente::

	SELECT 
		tc.table_name, kc.column_name
	FROM
		information_schema.table_constraints tc
		JOIN information_schema.key_column_usage kc 
		on kc.table_name = tc.table_name AND kc.table_schema = tc.table_schema
	WHERE
		tc.constraint_type = 'PRIMARY KEY'
	ORDER BY
		tc.table_name,
		kc.position_in_unique_constraint;