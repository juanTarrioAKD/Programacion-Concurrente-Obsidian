
1. Suponga que N clientes llegan a la cola de un banco y que serán atendidos por sus empleados. Analice el problema y defina qué procesos, recursos y canales/comunicaciones serán necesarios/convenientes para resolverlo. Luego, resuelva considerando las siguientes situaciones: 
	a. Existe un único empleado, el cual atiende por orden de llegada. 
	b. Ídem a) pero considerando que hay 2 empleados para atender, ¿qué debe modificarse en la solución anterior? 
	c. Ídem b) pero considerando que, si no hay clientes para atender, los empleados realizan tareas administrativas durante 15 minutos. ¿Se puede resolver sin usar procesos adicionales? ¿Qué consecuencias implicaría?


### a.

````ruby
chan eniviar_monto(real);
chan obtener_dinero[N](real);

process Cliente [id:1..N]{
	Real monto_aRetirar;
	Real dinero;
	obtener_monto(monto_aRetirar);
	send enviar_monto(id,monto_aRetirar);
	receive obtener_dinero[id](dinero);
}

process Empleado{
	Real monto_aRetirar;
	Real dinero;
	Int idCliente;
	for i:=1 to N{
		receive enviar_monto(idCliente,monto_aRetirar);
		retirar_dinero(monto_aRetirar,dinero);
		send obtener_dinero[idCliente](dinero);
	}
}
````

*Explicación:* Mantenemos 1 canal único para llamar al empleado (ya quedan ordenados por orden de llegada), el empelado va a obtener el monto que quiere retirar el cliente junto con su id. Luego llama a una función para sacar el dinero y en el canal privado del cliente que hizo el pedido le va a enviar el dinero.

---
### b.

````ruby
chan eniviar_monto(real);
chan obtener_dinero[N](real);

process Cliente [id:1..N]{
	Real monto_aRetirar;
	Real dinero;
	obtener_monto(monto_aRetirar);
	send enviar_monto(id,monto_aRetirar);
	receive obtener_dinero[id](dinero);
}

process Empleado [id:1..2]{
	Real monto_aRetirar;
	Real dinero;
	Int idCliente;
	while (true){
		receive enviar_monto(idCliente,monto_aRetirar);
		retirar_dinero(monto_aRetirar,dinero);
		send obtener_dinero[idCliente](dinero);
	}
}

````

*Explicación:* Con respecto al inciso "a" no modifica mucho, ya que los clientes no saben que empleado los va a atender por lo tanto siguen enviando en un canal único que los empleados van a ir recibiendo por orden de llegada los pedidos. Pero no se sabe cuantos pedidos va a atender cada empleado, por lo tanto en vez de un for se utiliza un while (true).

---
### c.

````ruby
chan enviar_monto(Int,Real);
chan obtener_dinero[N](Real);
chan solicitar_cliente(Int);
chan enviar_cliente[2](Int,Real);

process Cliente [id:1..N]{
	Real monto_aRetirar;
	Real dinero;
	obtener_monto(monto_aRetirar);
	send enviar_monto(id,monto_aRetirar);
	receive obtener_dinero[id](dinero);
}

process Coordinador{
	Int idCliente;
	Int idEmpleado;
	Real monto_aRetirar;
	while (true){
		receive solicitar_cliente(idEmpleado);
		if (not empty (enviar_monto)){
			receive enviar_monto(idCliente,monto_aRetirar);
		}
		else {
			idCliente=-999;
			monto_aRetirar=-999;
		}
		send enviar_cliente[idEmpleado](idCliente,monto_aRetirar);
	}
}

process Empleado [id:1..2]{
	Int idCliente;
	Real monto_aRetirar;
	Real dinero;
	while (true) {
		send solicitar_cliente(id);
		recieve enviar_cliente[id](idCliente,monto_aRetirar);
		if (idCliente==-999){
			tarea_administrativa(Delay(15*60));
		}
		else{
			retirar_dinero(monto_aRetirar,dinero);
			send obtener_dinero[idCliente](dinero);
		}
	}
}
````

*Explicación:* En este caso, es necesario un Coordinador ya que si no hay clientes deben hacer tareas administrativas. En caso de no tener el administrador y hacer el checkeo en Empleado causa demora innecesario, imagina el caso que hay 1 solo pedido en el canal de enviar_monto(), el empleado[1] pregunta el canal enviar_monto() no esta vacío? le da TRUE (hay 1 pedido), luego consulta el empleado[2] TAMBIEN LE DA TRUE. El error hay 1 solo pedido, 1 solo de los 2 empleados va a obtener el pedido mientras que el otro empelado se va a quedar esperando en el recieve enviar_monto() cuando en realidad tendría que realizar tareas administrativas. Por eso es necesario un Coordinador con canales privados a los empleados para que el canal sea de 1 solo proceso impidiendo que algún otro proceso saque el pedido del canal.
