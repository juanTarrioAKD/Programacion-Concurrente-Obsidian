
10. 
	En un parque hay un juego para ser usada por N personas de a una a la vez y de acuerdo al orden en que llegan para solicitar su uso. Además, hay un empleado encargado de desinfectar el juego durante 10 minutos antes de que una persona lo use. Cada persona al llegar espera hasta que el empleado le avisa que puede usar el juego, lo usa por un tiempo y luego lo devuelve. Nota: suponga que la persona tiene una función Usar_juego que simula el uso del juego; y el empleado una función Desinfectar_Juego que simula su trabajo. Todos los procesos deben terminar su ejecución.

````c
Process persona [id: 1 to N]{
	Sala_espera.esperar_llamado(id);
	Sala_deJuego.esperar_limpieza();
	Usar_juego();
	Sala_deJuego.avisar_salida();
}

Process empleado {
	for i := 1 to N {
		Sala_espera.esperar_persona();
		Desinfectar_Juego();
		Sala_espera.dejar_pasar();
	}
}

Monitor Sala_espera {
	cond espera_persona[N];
	cond espera_empleado;
	
	procedure esperar_llamado(in int id){
		if (empleado_esperando){
			signal(espera_empleado);
			empleado_esperando=false;
		}
		else{
			push(cola(id));
			wait(espera_persona[id])
		}
	}
	
	procedure esperar_persona(){
		if (cola.isempty()){
			empleado_esperando=true;
			wait(espera_empleado);
		}
		pop(cola(aux_id));
		signal(aux_id);
	}
}

Monitor Sala_juego {
	cond espera_empleado;
	cond espera_persona;
	bool termino_limpieza=false;
	bool llego_persona=false;
	
	procedure esperar_limpieaza(){
		if (!termino_limpieza){
			llego_persona=true;
			wait(espera_persona);
		}
		else{
			signal(espera_empleado);
		}
	}
	
	procedure dejar_pasar(){
		if (!llego_persona){
			termino_limpieza=true;
			wait(espera_empleado);
		}
		signal(espera_persona);
		wait(espera_empleado)
	}
	
	procedure avisar_salida(){
		signal(espera_empleado);
	}
}




````