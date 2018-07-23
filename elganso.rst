ElGanso
=======

Búsqueda y corrección de vales que no sincronizan
-------------------------------------------------

Para buscar vales que no se han sincronizado, ejecutaremos la siguiente qry::

	select count(*) from tpv_comandas c left outer join tpv_sincrodevoltienda s on c.idtpv_comanda = s.idtpv_comanda where c.ptesaldo = false and c.total < 0 and s.idtpv_comanda is null and c.saldopendiente > 0 and c.fecha >= '01-01-2015';

Y en caso de obtener resultados, podemos corregirlo con el siguiente update::

	update tpv_comandas set ptesaldo = true where idtpv_comanda in (select c.idtpv_comanda from tpv_comandas c left outer join tpv_sincrodevoltienda s on c.idtpv_comanda = s.idtpv_comanda where c.ptesaldo = false and c.total < 0 and s.idtpv_comanda is null and c.saldopendiente > 0 and c.fecha >= '01-01-2015');