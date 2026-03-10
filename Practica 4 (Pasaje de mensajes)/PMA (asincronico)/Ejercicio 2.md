
2. Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada por los cajeros. Luego del pago, se les entrega un comprobante. Nota: maximizar la concurrencia.


````ruby
chan pedido;
chan solicitar_caja(Int);
chan obtener_caja[P](Int);
chan solicitar_dinero[5](Int,Real);
chan obtener_dinero[P](Real);

process Cliente [id:1..P]{
	Int idCaja;
	Real dinero;
	Real monto;
	send solicitar_caja(id);
	send pedido;
	receive obtener_caja[id](idCaja);
	send solicitar_dinero[idCaja](id,monto);
	receive obtener_dinero[id](dinero);
	send pase(idCaja);
	send pedido;
}

process Coordinador {
	Int [5] filas; "vector simulando gente en filas"
	Int idCliente;
	Int idCaja;  
	while (true){
		receive pedido;
		if (not empty(pase)){
			receive pase(idCaja);
			filas[idCaja]--:
		}
		else (not empty(solicitar_caja)){
			receive solicitar_caja(idCliente);
			obtener_filaMinima(filas,idCaja)
			filas[idCaja]++;
			send obtener_caja[idCliente](idCaja);
		}
	}
}

process Caja [id:1..5]{
	Int idCliente;
	Real monto;
	Real dinero;
	while (true){
		receive solicitar_dinero[id](idCliente,monto);
		sacar_dinero(monto,dinero);
		send obtener_dinero[idCliente](dinero);
	}
}
````

*Explicación:* En este ejercicio manejo un coordinador que se encarga de contar a los clientes que están en las filas. El cliente va a solicitar la caja con menos gente, el coordinador se la va a enviar, luego el cliente ya sabe a que caja tiene que ir y se queda esperando en el canal solicitar_dinero() hasta que le toque su turno. Una vez que ya saco el dinero le avisa al coordinador que ya salió de la fila.
Otra forma de avisar que salió de la fila seria que la caja una vez que lo atiende llame al coordinador para decrementar la fila (eso ya depende de que es la fila, el cliente que esta sacando la plata esta en la fila? en caso de estarlo decrementa cuando ya saco la plata, si no es parte de la fila debería decrementar la caja cuando recibe al cliente).




