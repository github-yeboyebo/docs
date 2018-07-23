Magento
========

Sincronización y cambio de estado de los pedidos
-------------------------------------------------


**Estados sincronizados con AbnaQ**

Los posibles estados en los que puede estar un pedido de magento son:

* canceled
* closed
* complete
* holded
* pending_payment
* processing

A la hora de sincronizar pedidos con abanq se sincronizan sólamente los pedidos que están en cierto estado. Para ver qué estados son los que deben sincronizarse podemos hacerlo accediendo desde la administración de magento a *Sistema->Configuración->Intecativ4->Sync Order->Mapper From Magento->Estado del pedido*. Ej. En el ganso los estados son *complete* y *processing*. 


**Obtener el id a partir del código**

Si tenemos el código de un pedido de magento del tipo 120072938. Para obtener su id podemos hacerlo de dos formas:

* Desde la administracion de la web buscamos el pedido en *Ventas->Pedidos* por la columna *Pedido #* editamos el pedido y en la url copiamos lo que hay despues de *id*.

* Accediendo desde una consola a la base de datos ejecutamos lo siguiente:

``select entity_id from sales_flat_order where increment_id = '120072938';``


**Ver y marcar como exportado o no exportado**

Para saber si un pedido ha sido marcado como exportado.

``select is_exported from sales_flat_order where entity_id = id;``


Para marcar un pedido como no exportado::

	update sales_flat_order set is_exported = 0 where increment_id in (lista_de_codigos);
	o
	update sales_flat_order set is_exported = 0 where entity_id in (lista_de_ids); 


**Tablas relacionadas con el pedido**

Para cambiar el estado de un pedido hay que hacerlo en varias tablas::

* sales_flat_order - Tabla de pedidos
* sales_flat_order_grid - Tabla en el admin con la lista de pedidos
* sales_flat_order_status_history - Historico de estado de pedidos
* sales_flat_order_item - Lineas de pedido


**Consultas para cambair el estado**

Actulalizar estado::
	
	update sales_flat_order set state = 'estado', status = 'estado' where entity_id in (lista_de_ids);
	update sales_flat_order_grid set status = 'estado' where entity_id entity_id in (lista_de_ids);
	insert into sales_flat_order_status_history 	(parent_id,is_customer_notified,is_visible_on_front,comment,status,entity_name,created_at) values (id,0,0,'','estado','order',CURRENT_DATE);


Si pasa de cancelado a otro estado, hay que poner cantidad cancelada a 0::

	update sales_flat_order_item set qty_canceled = 0 where order_id = id;

Si pasa de otro estado a cancelado, hay que poner cantidad cancelada igual a la cantidad pedida::

	update sales_flat_order_item set qty_canceled = qty_ordered where order_id = id;


Seleccionar attributos de producto
-----------------------------------

Para obtener los ids de attributo de producto y el tipo de dato lo hacemos con::

	SELECT attribute_code, attribute_id, backend_type  FROM eav_attribute WHERE entity_type_id=4 order by backend_type;


El backend_type nos indica en qué tabla debemos buscar el attributo. La tabla sería catalog_product_entity_backend_type. Ej. Para obtener el attributo talla::

	SELECT attribute_code, attribute_id, backend_type FROM eav_attribute WHERE entity_type_id=4 and attribute_code = 'size';

	+----------------+--------------+--------------+
	| attribute_code | attribute_id | backend_type |
	+----------------+--------------+--------------+
	| size           |          118 | int          |
	+----------------+--------------+--------------+

	select * from catalog_product_entity_int where attribute_id = 118;

	

Borrar tallas repetidas
------------------------

1. Obtenemos el attribute_id de la talla con::

	SELECT * FROM eav_attribute WHERE entity_type_id=4 and attribute_code = 'size';

2. Seleccionamos qué tallas están repetidas con::

	select count(value), value from eav_attribute_option_value where option_id in (SELECT value FROM catalog_product_entity_int WHERE attribute_id=118) group by value having count(value) > 4;

3. Seleccionamos los ids de cada tabla con::

	SELECT * FROM eav_attribute_option_value WHERE value = 10;

4. Vemos cuantos artículos hay en cada talla para cada id con::

	SELECT count(*),value FROM catalog_product_entity_int WHERE attribute_id=118 and value in (30,72,78) group by value;

5. Actualizamos todos los ariculos a una misma talla con::

	update catalog_product_entity_int set value = 30 WHERE attribute_id=118 and value in (72,78);

Repetimos esto para todas las tallas.

6. Para borrar las opciones no necesarias::

	delete from eav_attribute_option_value WHERE option_id IN (72,78,59,66,29,31,28,27);

Y en el admin borrar las que quedan en blanco en la opción de atributos



Problemas con extension SortProducts
-------------------------------------


Si al reordenar se borran productos hay que ampliar el límite de variable poniendo en .htacces::

	php_value max_input_vars 4000
	php_value suhosin.get.max_vars 4000
	php_value suhosin.post.max_vars 4000
	php_value suhosin.request.max_vars 4000




Problemas con Google Analytics
-------------------------------

Quitar la opción *System->Configuration->Web->Session Cookie Management->Cookie Restriction Mode*



Seleccionar stocks con distinta visibilidad en las dos tientas
---------------------------------------------------------------

Para ver si hay productos que se muestran con stock diferente en distintas tiendas::

	select p.sku, c1.product_id, c1.stock_status, c2.product_id, c2.stock_status from cataloginventory_stock_status c1 inner join cataloginventory_stock_status c2 on c1.product_id = c2.product_id and c1.stock_status <> c2.stock_status and c1.website_id = 1 inner join catalog_product_entity p on p.entity_id = c1.product_id order by p.sku;


Para actualizra el estado del stock::

	update cataloginventory_stock_status set stock_status = 1 where product_id in (lista_id_proudtos) and website_id = id_website;



Seleccionar datos de pedidos para una referencia en concreto
-------------------------------------------------------------

Consulta::

	select o.created_at, o.updated_at, i.qty_ordered,i.qty_canceled,i.order_id from sales_flat_order_item i INNER JOIN sales_flat_order o on i.order_id = o.entity_id where i.sku = 'sku' order by o.updated_at desc limit 50;




Problemas con BrainSins
------------------------

Copiar estas lineas al .htaccess::

	############################################
	## por defecto voy a redireccionar las apis a
	## /es todo para evitar probl. brainsins.

	RewriteRule ^(api.*) /es/$1 [QSA,L,R=301]
	RewriteRule ^index.php/(api.*) /es/$1 [QSA,L,R=301]    
	
	############################################




Encontrar pedidos sin movimiento de puntos
-------------------------------------------

Hay que crear una bd con las dos tablas y ejecutar::

	select created_at, status from sales_flat_order left outer join tpv_movpuntos on sales_flat_order.entity_id = tpv_movpuntos.idpedidomagento where discount_description like '%puntos utilizados%' and tpv_movpuntos.idpedidomagento is null order by created_at;




Problemas al acceder al admin
-------------------------------

* Borrar de la tabla core_config_data el valor web/cookie/cookie_domain

* Mofidicar fichero *app/code/core/Mage/Core/Model/Session/Abstract/Varien.php*::

	// session cookie params
	 	$cookieParams = array(
	        	'lifetime' => $cookie->getLifetime(),
	        	'path' 	=> $cookie->getPath()//,
	        	//'domain'   => $cookie->getConfigDomain(),
	        	//'secure'   => $cookie->isSecure(),
	        	//'httponly' => $cookie->getHttponly()
	    	);




Cambiar idioma de las descripciones de los productos
------------------------------------------------------

1. Borramos los attributos de descripción para una tienda. Al hacer esto se marcará la opción de usar valor por defecto::

	delete from catalog_product_entity_text where store_id = id_tienda and attribute_id in (61,62,72);
	delete from catalog_product_entity_varchar where store_id = id_tienda and attribute_id in (61,62,72);


2. Insertamos registros copiando los de otra tienda::

	insert into catalog_product_entity_text (entity_type_id, attribute_id, store_id, entity_id, value) select 4, attribute_id, id_tienda_a_actualizar, entity_id, value from catalog_product_entity_text where store_id = id_tienda_copiada and attribute_id in (61,62,72);
	insert into catalog_product_entity_varchar (entity_type_id, attribute_id, store_id, entity_id, value) select 4, attribute_id, id_tienda_a_actualizar, entity_id, value from catalog_product_entity_varchar where store_id = id_tienda_copiada and attribute_id in (60,71,73);



Cambiar idioma de las descripciones de las categorias
------------------------------------------------------

1. Borramos los attributos de descripción para una tienda. Al hacer esto se marcará la opción de usar valor por defecto::

	delete from catalog_category_entity_varchar where store_id = id_tienda and attribute_id in (33,35,38);

2. Insertamos registros copiando los de otra tienda::

	insert into catalog_category_entity_varchar (entity_type_id, attribute_id, store_id, entity_id, value) select 3, attribute_id, id_tienda_a_actualizar, entity_id, value from catalog_category_entity_varchar where store_id = id_tienda_copiada and attribute_id in (33,35,38);




Actualizar precios
-------------------

1. Borramos el attributo precio para una tienda. Al hacer esto se marcará la opción de usar valor por defecto::

	delete from catalog_product_entity_decimal where store_id = id_tienda and attribute_id = 64;


2. Insertamos registros copiando los de otra tienda::

	insert into catalog_product_entity_decimal (entity_type_id, attribute_id, store_id, entity_id, value) select 4, attribute_id, id_tienda_a_actualizar, entity_id, value from catalog_product_entity_decimal where store_id = id_tienda_copiada and attribute_id in (64);




Borrar caché servidor
----------------------

Acceder a través de ssh al servidor 1 y ejecutar::

	redis-cli -h mgt-cache.yzrydd.0001.euc1.cache.amazonaws.com 

	y después flushall


Actualizar la fecha y hora de los banners
------------------------------------------

Desde el admin no se puede establecer la hora, por defecto pone siempre la hora actual. Es necesario poner la hora para activar los banners para un dia en concreto por lo que la cambiamos desde una consola. Primero identificamos los ids abriendo la tabla en el admin en el menú Banner Slider. Debemos poner una hora menos porque el servidor de la web tiene una hora de retraso Ej::

	update bannerslider_banner set start_time = '2015-11-26 23:00:00', end_time = '2015-11-27 22:59:59' where banner_id in (106,107,108,109,110,111,112);




Seleccionar pedidos con mensaje para regalo entre fechas
-----------------------------------------------------------

Acceder a través de ssh al servidor 1 y ejecutar::

	select increment_id from sales_flat_order where gift_message_id is not null and gift_message_id <> 0 and created_at >= '2015-11-27 00:00:00' and created_at <= '2015-11-29 23:59:59';




Actualizar la tasa de conversion
---------------------------------

Para actualizar la tasa de conversión (euros-libras) debemos seguir los siguientes pasos:

1. Actualizar precios ya existentes en la base de datos. Conectamos a la BBDD de producción y lanzamos la siguiente consulta::
	
	update catalog_product_entity_decimal set value = value/(TASA_NUEVA/TASA_ANTIGUA) where store_id = ID_TIENDA and attribute_id = ID_ATRIBUTO_PRECIO;



donde:
	ID_TIENDA es el id de la tienda (para intl_uk es 7)

	ID_ATRIBUTO_PRECIO el el id del atributo precio (suele ser 64)


2. Cambiar la tasa de conversión en magento:

	Se modifica en Sistema->Manage Currency->Tarifas


3. Reindexar los indices:

	Sistema->Indexes Management

		Marcar Product Prices y Product Flat Data, en Acciones seleccionar Reindex Data y darle a enviar


4. Modificar la tasa de conversión en AbanQ

	Area de Facturación -> + -> Websites

		Seleccionar intl->intl_uk->Tarifa->Tasa Conversión

	Guardar todo.



Sincronizar ventas tras un parón en las sincro
-----------------------------------------------------------

Cuando se dejan de importar pedidos web durante más de un día, como los ficheros ORDERS y CUSTOMERS pasan a la carpeta BK es necesario volver a moverlos a la carpeta IN para que sean procesados. Esos directorios están en el servidor central de elganso en: /home/elganso/web/erp/IN/

Además si un fichero se ha intentado procesar y ha fallado se crea un registro en una tabla de ficheros procesados. Para poder procesarlo de nuevo además de moverlo de carpeta habría que borrar el registro en esa tabla. La tabla está en Almacén -> Más -> Magento -> Importación y Exportación -> Pestaña Importación



Incluir, quitar tiendas de la web (recogida en tienda)
--------------------------------------------------------

**Quitar una tienda**

En el fichero  app/code/local/Infosial/Storepickup/Block/Onepage/Abstract.php línea 42, añadir la excepción por código de tienda a la consulta.
Ej::

	$whereTiendas = 't.sincroactiva AND (t.direccion IS NOT NULL AND t.direccion <> "") AND (t.ciudad IS NOT NULL AND t.ciudad <> "") AND t.idprovincia NOT IN (8, 60, 35, 78, 62, 43) AND (t.codtienda = "AETM" or t.codtienda not like "AE%") and t.codtienda <> "AGLY" and t.codtienda <> "AGLT"';


No muestra las tiendas AETM, AGLY, AGLT y las que empiezan por AE.

**Mostrar tiendas por storeview**

En el fichero  app/code/local/Infosial/Storepickup/Block/Onepage/Abstract.php línea 72, hay un switch de paises permitidos por storeview.


**Incluir tienda que no se muestra**

1. Comprobar que la tienda existe en Abanq en Facturación->Tpv->Tiendas. o por consola en la tabla tpv_tiendas

	- La tienda debe tener informados todos los campos que en la web son obligatorios (codigo postal, ciudad, pais, tipo de vía, dirección, y provincia)
	- La tienda debe tener informados los campos provincia e idprovincia
	- Buscamos la provincia en Facturación->Principal->Provincias o por consola en provincias y debe tener informado el campo id Magento (mg_idprovinca).
		Si no lo tiene hay que informarlo buscando la provincia correspondiente en magento.
		Para ello abrimos la bd de la we en el servidor de producción y hacemos un select sobre la tabla directory_country_region, buscamos el registro que tenga default_name el nombre de la provincia y cogemos como id el region_id. 
		Si no existe la provincia en esa tabla la cremaos con un insert.

2. Comprobar que la tienda se ha sincronizado correctamente en la web en la bd elganso_points tablas tpv_tiendas y provincias. Si no están sincronizadas debemos o forzar la sincro (para ello preguntar a Santi o Jesús) o informarlos directamente con updates.

3. Comprobar que ya aparece en la web.


