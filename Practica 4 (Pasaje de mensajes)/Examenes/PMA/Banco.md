
![](../../../Imagenes/Imagen12.png)


````ruby
chan pedido[3]();
chan solicitar_prioridad[3](Int);
chan solicitar_sinPrioridad[3](Int);
chan 

process Cliente [id:1..N]{
	Int idCaja;
	obtener_caja(idCaja);
	if ("soy especial"){
		send soliciar_prioridad[idCaja](id);
	}	
	else send solicitar_sinPrioridad[idCaja](id);
	send pedido[idCaja]();
	receive recibir_comprobante[id](comprobante);
}


process Caja [id:1..3]{
	while (true){
		recieve pedido[id]();
		if (not empty(solicitar_prioridad[id])){
			receive solicitar_prioridad[id](idCliente);
		}
		else receive solicitar_sinPrioridad[id](idCliente);
		atender_cliente(idCliente);
		generar_comprobante(comprobante);
		send recibir_comprobante[idCliente](comprobante);		
	}
}
````
