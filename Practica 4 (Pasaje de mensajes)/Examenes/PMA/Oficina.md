
Resolver con PMA el siguiente problema. En una oficina hay 3 empleados y P personas que van para ser atendidas para realizar un tramite. Cuando una persona llega espera hasta ser atendido por cualquiera de los empleados, le indica el tramite a realizar y esperar a que le den el resultado. Los empleados atienden solicitudes en orden de llegada, si no hay personas esperando, durante 5 minutos resuelven tramites pendientes (simular el proceso de resolver tramites pendientes medio de un delay). Nota: No generar demora innecesaria, cada persona hace solo un pededido y termina, los empleados no necesitan terminar su ejecucion.

### solucion mala:

````ruby
chan pedido(int)
chan solicitar_empleado();
chan solicitar_persona();
chan enviar_empleado[P](int);
chan enviar_tramite[3](text);
chan solicitar_resultado[P](text);

process Persona [id:1..P]{
	int idEmpelado;
	text tramite;
	text resultado;
	send pedido(id);
	send solicitar_empleado();
	receive enviar_empleado[id](idEmpleado);
	generar_tramite(tramite);
	send enviar_tramite[idEmpleado](tramite);
	recieve solicitar_resultado[id](resultado);
}

process Coordinador {
	cola buffer;
	while (true){
		receive pedido(id);
		if (not empty (solicitar_empelado)){
			receive solicitar_empleado();	
			push(buffer, id);
			}
		}
		▢ receive enviar_persona();
		  if (not empty(buffer)){
			  pop(buffer, idPersona);
		  }
		  else
			  idPersona=-999;
		  send enviar_persona[id](idPersona);		  
	}
}

process Empleado [id:1..3]{
	int idPersona;
	text tramite;
	text resultado;
	while (true){
		send pedido(id);
		send solicitar_persona();
		receive enviar_persona[id](idPersona);
		if (idPersona==-999){
			delay(5*60);
		}
		else{
			send enviar_empleado[idPersona](id);
			receive enviar_tramite[idPersona](tramite);
			solucionar(tramite,resultado);
			send solicitar_resultado[idPersona](resultado);
		}
	}
}
````

---
### solucion chichi

````ruby
chan pedido_empleado(int)
chan pedido_persona(int)
chan enviar_empleado[P](int);
chan enviar_tramite[3](text);
chan solicitar_resultado[P](text);

process Persona [id:1..P]{
	int idEmpelado;
	text tramite;
	text resultado;
	send pedido_persona(id);
	receive enviar_empleado[id](idEmpleado);
	generar_tramite(tramite);
	send enviar_tramite[idEmpleado](tramite);
	recieve solicitar_resultado[id](resultado);
}

process Coordinador {
	int idEmpleado;
	while (true){
		receive pedido_empleado(idEmpleado);
		if (not empty(pedido_persona)){
			receive pedido_persona(idPersona);
		}	  
		else
			idPersona=-999;
		send enviar_persona[idEmpleado](idPersona);
	}
}

process Empleado [id:1..3]{
	int idPersona;
	text tramite;
	text resultado;
	while (true){
		send pedido_empleado(id);
		receive enviar_persona[id](idPersona);
		if (idPersona==-999){
			delay(5*60);
		}
		else{
			send enviar_empleado[idPersona](id);
			receive enviar_tramite[idPersona](tramite);
			solucionar(tramite,resultado);
			send solicitar_resultado[idPersona](resultado);
		}
	}
}
````