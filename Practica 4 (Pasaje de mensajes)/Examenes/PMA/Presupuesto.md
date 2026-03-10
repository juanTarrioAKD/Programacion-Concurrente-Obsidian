
Reolver con PMA el siguiente problema. En un negocio hay 5 empleados que atienden a N personas que van a pedir un presupuesto, de acuerdo al orden de llegada. Cuando el cliente sabe que empleado lo va a atender le entrega el listado de productos que necesita, y luego el empleado le entrega el presupuesto del mismo. Nota maximizar la concurrencia.

````ruby
process Persona [id:1..N]{
	text lista;
	int idEmpleado;
	text presupuesto;
	generar_lista(lista)
	send solicitar_empleado(id);
	receive enviar_empleado[id](idEmpleado);
	send enviar_lista[idEmpleado](lista);
	receive enviar_presupuesto[id](presupuesto);
}

process Empleado [id:1..5]{
	int idPersona;
	text lista;
	text presupuesto;
	while (true){
		recieve solicitar_empleado(idPersona);
		send enviar_empleado[idPersona](id);
		recieve enviar_lista[id](lista);
		generar_presupuesto(lista,presupuesto);
		send enviar_presupuesto[idPersona](presupuesto);
	}
}
````