
4. 

	existen N vehículos que deben pasar por un puente de acuerdo con el orden de llegada. Considere que el puente no soporta más de 50000kg y que cada vehículo cuenta con su propio peso (ningún vehículo supera el peso soportado por el puente).

````c
	 process vehiculo (id:1..N){
		 real peso;
		 obtener_peso(peso);
		 puente.pasar(peso);
		 --pasando puente
		 puente.liberar();
	 }

	 Monitor Puente {
		cond espera[N];
		cola cola
		real peso_total = 0;
		bool seguir_sacando = true;
		
		procedure pasar (in real peso, in int id){
			if (peso_total+peso>50000){
				push (cola(id,peso));
				wait(espera[id]);
			}
			else{
				peso_total+=peso;
			}
		}

		procedure salir (in real mi_peso){
			peso_total-=mi_peso;
			while ((cola.isnotempty) && (seguir_sacando)){ // mientras entren 
				pop(cola,id_aux,peso_aux);  // lo saco
				if ((peso_aux + peso_total)<50000){ // si entra en en el puente
					signal(espera[id_aux]); // lo despierto
					peso_total+=peso_aux; // actulizo el peso
				}
				else {
					seguir_sacando=false; // no saco mas 
					insertar_adelante(cola,id_aux,peso_aux); // lo inserto donde estaba.
				}
			}
		}
	  }

````


