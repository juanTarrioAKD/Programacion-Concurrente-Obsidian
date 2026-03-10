
9. 
	 En un examen de la secundaria hay un preceptor y una profesora que deben tomar un examen escrito a 45 alumnos. El preceptor se encarga de darle el enunciado del examen a los alumnos cuando los 45 han llegado (es el mismo enunciado para todos). La profesora se encarga de ir corrigiendo los exámenes de acuerdo con el orden en que los alumnos van entregando. Cada alumno al llegar espera a que le den el enunciado, resuelve el examen, y al terminar lo deja para que la profesora lo corrija y le envíe la nota. Nota: maximizar la concurrencia; todos los procesos deben terminar su ejecución; suponga que la profesora tiene una función corregirExamen que recibe un examen y devuelve un entero con la nota.

````c
Process Preceptor (){
	text examen_preparado;
	tomar_examen(examen_preparado);
	Mesas.repartir_examen(examen_preparado);
}

Process Profesora (){
	text examen_alumno;
	int nota_alumno;
	for i:= 1 to 50{
		Escritorio.agarrar_examen(examen_alumno);
		corregirExamen(examen_alumno,nota_alumno);
		Escritorio.entregar_nota(nota_alumno);
	}
}

Process Alumno (){
	text exam;
	Mesas.obtener_examen(exam);
	realizar_examen(exam);
	Escritorio.esperar_resultado(exam,nota);
}

Monitor Mesas {
	cond espera;
	int cant_alumnos=0;
	text exam;
	
	procedure obtener_examen (out text tomar_examen){
		cant_alumnos++;
		if (cant_alumnos==50){
			signal(esperando_preceptor);
		}
		wait(esperar);
		tomando_examen=examen;
	}
	
	procedure repartir_examen (in text examen_preparado){
		if (cant_alumnos<50){
			wait(esperando_preceptor);
		}
		signalall(esperar);
		exam=examen_preparado;
	}
}

Monitor Escritorio {
	cond esperando_nota [45];
	cond esperando_profesora;
	cola cola_conExamen;
	cola cola_sinExamen;
	int notas [45];
	int aux_id;
	text aux_examen;
	
	
	procedure esperar_resultado(in text examen, out int nota, in int id){
		push(cola(id,examen));
		if (profesora_desocupada){
			signal(esperando_profesora);
			profesora_desocupada=false;
		}
		wait(esperando_nota[id]);
		nota=notas[id];
	}
	
	procedure tomar_examen (out text examen_aCorregir){
		if (cola_conExamen.isempty()){
			profesora_desocupada=true;
			wait(esperando_profesora);
		}
		pop(cola_conExamen(aux_id,aux_examen));
		push(cola_sinExamen(aux_id));
		examen_aCorregir=aux_examen;
	}
	
	procedure entregar_nota (in int nota_alumno){
		pop(cola_sinExamen(aux_id));
		notas[aux_id]=nota_alumno;
		signal(aux_id);	
	}
}

````
