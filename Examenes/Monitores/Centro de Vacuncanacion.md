
![[Tercer Anio/Concurrente/Imagenes/Imagen9.png]]


````c
process Paciente [id:1..200]{
	int centro;
	int edad;
	get_centro(centro);
	get_edad(edad);
	Vacunatorio[centro].sala_espera(edad,id);
	Silla[centro].esperar_vacuna();
}

process Empleado [id:1..8]{
	for i:= 1 to 25 {
		Vacunatorio[id].llamar_paciente();
		Silla[id].vacunar();
	}

}

Monitor Vacunatorio[id:1..8]{
	cond espera [200];
	colaPrioridad cola;
	bool libre = true;

	procedure sala_espera (in int edad, in int id){
		if (!libre){
			push.prioridadEdad(cola(id),edad);
			wait(espera[id]);
		}
		libre=false;
	}
	
	procedure llamar_paciente (){
		if (cola.isnotempty()){
			push(cola(aux_id));
			signal[aux_id];
		}
		else{
			libre=true;
		}
	}
}

Monitor Silla[id:1..8]{
	
	procedure esperar_vacuna (){
		llegue=true;
		signal(espera_enfermera);
		wait(espera_paciente);
		signal(salida);	
	}
	
	procedure vacunar (){
		if (!llego){
			wait(espera_enfermera);
		}
		vacunar();
		signal(espera_paciente);
		wait(salida);
	}
}

`````