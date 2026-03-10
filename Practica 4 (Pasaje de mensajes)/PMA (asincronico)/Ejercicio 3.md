
3. Se debe modelar el funcionamiento de una casa de comida rápida, en la cual trabajan 2 cocineros y 3 vendedores, y que debe atender a C clientes. El modelado debe considerar que: - Cada cliente realiza un pedido y luego espera a que se lo entreguen. - Los pedidos que hacen los clientes son tomados por cualquiera de los vendedores y se lo pasan a los cocineros para que realicen el plato. Cuando no hay pedidos para atender, los vendedores aprovechan para reponer un pack de bebidas de la heladera (tardan entre 1 y 3 minutos para hacer esto). - Repetidamente cada cocinero toma un pedido pendiente dejado por los vendedores, lo cocina y se lo entrega directamente al cliente correspondiente. Nota: maximizar la concurrencia.

````ruby

process Cliente[id:1..C]{
	Text pedido;
	Text comida;
	seleccionar_comida(pedido);
	send enviar_pedido(id,pedido);
	receive obtener_menu[id](comida);
}

process Coordinador{
	Int idCliente;
	Int idVendedor;
	Text pedido;
	while (true){
		receive solicitar_cliente(idVendedor);
		if(not empty(enviar_pedido)){
			receive enviar_pedido(idCliente,pedido);
		}
		else{
			idCliente=-999;
			pedido="invalido";
		}
		send enviar_cliente[idVendedor](idCliente,pedido);
	}
}

process Vendedor [id:1..3]{
	Int idCliente;
	Text pedido;
	while (true){
		send solicitar_cliente(id);
		receive enviar_cliente[id](idCliente,pedido);
		if (idCliente==-999){
			recargar_bebidas(Delay(3*60));
		}
		else{
			send enviar_cocina(idCliente,pedido);
		}
	}
}

process Cocinero [id:1..2]{
	Int idCliente;
	Text pedido;
	Text comida;
	while (true){
		recieve enviar_cocina(idCliente,pedido);
		cocinar_pedido(pedido,comida);
		send obtener_menu[idCliente](comida);
	}
}
````

*Explicación:* En este ejercicio es necesario un coordinador entre el cliente y el vendedor por la misma razón del ejercicio 2, en caso de no haber pedidos tienen que realizar otra cosa, y en caso de haber 1 solo pedido si consultas directamente desde el vendedor te retorna TRUE el not empty enviar_pedido() y solo 1 lo va a obtener, el otro se queda trabado en el recieve cuando tendría que reponer bebidas. En caso del cocinero no es necesario ya que el vendedor envía en un canal único los pedidos y los cocineros van a ir agarrándolos y cocinando (en caso de tener que hacer otra acción si no hay pedidos como limpiar la cocina o algo por el estilo SI tendría que haber un coordinador entre el vendedor y el cocinero).

