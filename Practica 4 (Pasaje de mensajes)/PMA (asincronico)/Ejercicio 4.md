
4. Simular la atención en un locutorio con 10 cabinas telefónicas, el cual tiene un empleado que se encarga de atender a N clientes. Al llegar, cada cliente espera hasta que el empleado le indique a qué cabina ir, la usa y luego se dirige al empleado para pagarle. El empleado atiende a los clientes en el orden en que hacen los pedidos. A cada cliente se le entrega un ticket factura por la operación. 
	a) Implemente una solución para el problema descrito. 
	b) Modifique la solución implementada para que el empleado dé prioridad a los que terminaron de usar la cabina sobre los que están esperando para usarla. 
	Nota: maximizar la concurrencia; suponga que hay una función Cobrar() llamada por el empleado que simula que el empleado le cobra al cliente.

### a.

`````ruby
chan pedido;
chan solicitar_cabina(Int);
chan obtener_cabina[N](Int);
chan dejar_cabina(Int);

process Cliente [id:1..N]{
	Int idCabina;
	send solcitar_cabina(id);
	send pedido;
	receive obtener_cabina[id](idCabina);
	utilizar_cabina(idCabina);
	send dejar_cabina(idCabina);
	send pedido;
}

process Empleado {
	Bool [10] cabinas = ([10],true);
	Cola buffer(Int);
	Int idCabina;
	Int idCliente;
	while (true){
		receive pedido;
		if (not empty(dejar_cabina)){
			recieve dejar_cabina(idC abina);
			if (not empty(buffer)){
				send obtener_cabina[pop(buffer)](idCabina);
			}
			else cabinas[idCabina]=true;
		}
		▢  (not empty(solicitar_cabina)){
				recieve solicitar_cabina(idCliente);
				obtener_cabina(cabinas,idCabina);
				if (idCabina==-999){
					push(buffer, idCliente);
				}
				else {
					send obtener_cabina[idCliente](idCabina);
					cabinas[idCabina]=false;
				}
			}
		}
	}
}
`````

*Explicación:* En este ejercicio manejo una vector de booleanos (representan a las cabinas libres) y una cola llamada buffer (representa a la gente esperando). Es necesario el buffer? Si, ya que voy a estar recibiendo solicitar_cabina() y capaz no hay cabinas disponibles por lo tanto tengo que encolarlo y luego cuando alguien deje una cabina (dejar_cabina()) voy a hacer un pasing the condition desencolando al cliente y enviándole la cabina que están liberando (en caso de estar vacío el buffer, es decir no hay clientes esperando cabinas, la marco como liberada). Se podría darle prioridad a la gente que deja la cabina con un IF ELSE, para no estar encolando a gente cuando la cabina ya esta libre, pero el enunciado no solicita prioridad.