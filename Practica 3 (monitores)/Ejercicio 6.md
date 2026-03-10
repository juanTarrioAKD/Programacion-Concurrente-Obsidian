
6. 
	Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función AsignarNroGrupo() que retorna un número “aleatorio” del 1 al 25. Cuando un alumnoa recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1). Nota: el JTP no guarda el número de grupo que le asigna a cada alumno.

	 Process alumno [id:1..50]{
		 Tarea.llegar(id,tema);
		 -- hacer mi tarea
		 Tarea.Entregar(id,tema);
		 -- ver resultado
	 }

	 Process JTP {
		 for i := 1 to 50 {
			 Tarea.repartir_examen();
		 }
		 for i := 1 to 50 {
			 Tarea.dar_notas();
		 }
	 }

	 Monitor T (){
		int grupos [25] = ([25], 0);
		int mi_tema [50];
		cola cola;
		cond espera_JTP;
		cond espera_alumno[50];
		int cant = 0;
		bool JTP_esperando=false;
		int tema;
		int aux_id;
		int aux_tema;
		matriz [25] grupo_esperando [2];
		int nota = 25;
		
		procedure llegar (in int id,out int tema){
			cant++;
			if (cant=50){ // si soy el ultimo despierto al JTP
				signal(esperando_JTP);
			}
			push(cola,id);
			wait(espera_alumno[id]); // espero a que me den mi tarea
			tema=mi_tema[id];        // obtengo mi tema del vector (esta en vec[mi_id])
			signal(espera_JTP);      // despierto al JTP y me voy a hacer mi tarea
		} 

		procedure repartir_examen (){
			if (cant<50){  // si no estan todos me duermo
				wait(espera_JTP);
			}
				AsignarNroGrupo(tema); 
				pop(cola,aux_id);
				mi_tema[aux_id]=tema; // le asigno el tema en un vector 
				signal(espera_alumno[aux_id]); // despierto al alumno 
				wait(espera_JTP); // espero a que obtenga su examen
		}

			procedure Entregar (in int id, in int tema,out int resultado){				         signal(esperando_JTP);
			grupos[tema]++; // incremento cantidad que termino de mi grupo
			push(cola,id,tema); 
			wait(esperando_alumno[id]); // espero la nota
			resultado=grupos[tema]; // agarro mi nota
		}
		
		procedure dar_notas (){
			if (cola.isnotempty()){
				JTP_esperando = true;
				wait(esperando_JTP);
			}
			pop(cola,aux_id,aux_tema); // lo saco de la cola 
			grupos[aux_tema]++; // incremento la cantidad que termino de ese tema
			push(grupo_esperando[tema],aux_id); // guardo el id del que termino
			if (grupos[aux_tema] = 2){ // si ya terminaron los 2
				while (grupos[aux_tema].isnotempty()){ // despierto al grupo
					pop(grupo_esperado[aux_tema],aux_id);
					signal(espera_alumno[aux_id]); 
				}
				grupos[aux_tema]=nota; // les doy nota 
				nota-- // decremento nota
				}
			}
		}
	 }