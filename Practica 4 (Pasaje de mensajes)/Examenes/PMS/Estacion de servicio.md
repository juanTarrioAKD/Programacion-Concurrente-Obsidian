Resolver con PMS. Simular la atención de una estación de servicio con un único surtidor que tiene un empleado que atiende a los N clientes de acuerdo al orden de llegada. Cada cliente espera hasta que el empleado lo atienda y le indica que y cuanto cargar, espera hasta que termina de cargarle el combustible y se retira. Nota: cada cliente carga combustible solo una vez, todos los procesos deben terminar.

````ruby
process Cliente [id:1..N]{
	Text combustible;
	Int cantidad;
	seleccionar_combustuble(combustible);
	seleccionar_cantidad(cantidad);
	Admin!solicitar_carga(id);
	Empleado?solicitar_datos();
	Empleado!enviar_datos(combustible,cantidad);
	Empleado?retirarse();
}

process Admin {
	Int idCli;
	Text combustible;
	Int cantidad;
	Int total=0;
	Cola buffer(Int);
	do (total<=N); Cliente[*]?solicitar_carga(idCli) -> 
									  push(buffer,idCLi);
									  total++;
	▢  (not empty(buffer)); Empleado?solicitar_cliente(); ->
							Empleado!enviar_cliente(pop(buffer));
	od
	Empleado?solicitar_cliente() -> Empelado!enviar_cliente(-999);
}

process Empleado {
	Int idCliente;
	Text combustible;
	Int cantidad;
	Bool seguir=true;
	while (seguir){
		Admin!solicitar_cliente();
		Admin?enviar_cliente(idCliente);
		if (idCliente==-999){
			seguir=false;
		}
		else{
			Cliente[idCliente]!solicitar_datos();
			Cliente[idCliente]?enviar_datos(combustible,cantidad);
			cargar_combustible(combustible,cantidad);
			Cliente[idCliente]!retirarse();
		}
	}
}
````

*Explicación:* En este ejercicio hay que manejar un Admin para ir encolando a los clientes a medida que llegan (si no lo manejamos el empleado mientras carga combustible no va a poder ir atendiendo a los clientes y después de forma no determinística va a atender a 1 que puede ser el ultimo que llego). El admin encola a los clientes, y el empleado pide clientes (se maximiza la concurrencia ya que mientras el empleado esta cargando combustible los clientes se están encolando).