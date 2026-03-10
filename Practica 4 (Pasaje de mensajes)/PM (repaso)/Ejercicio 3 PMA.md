
3) Resolver el siguiente problema con PMA. En un negocio de cobros digitales hay P personas que deben pasar por la única caja de cobros para realizar el pago de sus boletas. Las personas son atendidas de acuerdo con el orden de llegada, teniendo prioridad aquellos que deben pagar menos de 5 boletas de los que pagan más. Adicionalmente, las personas embarazadas tienen prioridad sobre los dos casos anteriores. Las personas entregan sus boletas al cajero y el dinero de pago; el cajero les devuelve el vuelto y los recibos de pago.

````ruby
chan pedido;
chan enviar_embarazada(real,text);
chan enviar_menosboletas(real,text);
chan enviar_masboletas(real,text);
chan recibir_vuelto[P](text,real);

process Personas [id:1..P]{
	int cantidad;
	real monto;
	real vuelto;
	text recibo;
	obtener_cantidad(cantidad)
	if ("soy embarazada"){
		send enviar_embarazada(id,monto);
	}
	else {
		if (cantidad<5){
			send enviar_menosboletas(id,monto);
		}
		else send enviar_masboletas(id,monto);
	}
	send pedido();
	receive recibir_vuelto[id](recibo,vuelto);
}

process Cajero {
	int idCliente;
	real monto;
	real vuelto;
	text recibo;
	while (true){
		recieve pedido();
		if (not empty(enviar_embarazada)){
			receive enviar_embarazada(idCliente,monto);
		}
		else {
			if (not empty(enviar_menosboletas)){
				receive enviar_menosboletas(idCliente,monto);
			}
			else {
				receive enviar_masboletas(idCliente,monto);
			}
		}
		cobrar(monto,vuelto,recibo);
		send recibir_vuelto[idCliente](recibo,vuelto);
	}
}
````

