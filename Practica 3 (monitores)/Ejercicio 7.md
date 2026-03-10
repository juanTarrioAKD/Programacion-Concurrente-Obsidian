
7.
	Se debe simular una maratón con C corredores donde en la llegada hay UNA máquina expendedoras de agua con capacidad para 20 botellas. Además, existe un repositor encargado de reponer las botellas de la máquina. Cuando los C corredores han llegado al inicio comienza la carrera. Cuando un corredor termina la carrera se dirigen a la máquina expendedora, espera su turno (respetando el orden de llegada), saca una botella y se retira. Si encuentra la máquina sin botellas, le avisa al repositor para que cargue nuevamente la máquina con 20 botellas; espera a que se haga la recarga; saca una botella y se retira. Nota: mientras se reponen las botellas se debe permitir que otros corredores se encolen.


````c
process Corredor [id: 1..C]{
	Carrera.esperar();
	--corre la carrera;
	Carrera.tomar_agua(botella);
	--tomar agua
}

process Repositor {
	cola maquina;
	while (true){
		Carrera.reponer_botellas(maquina);
		for i:= 1 to 20{
			botella_aux = tomar_botella();
			push(maquina,botella_aux);
		}
		Carrera.confirmar_carga(maquina);
	}
}

Monitor Carrera (){
	cond espera_corredor;
	cond espera_repositor;
	cond espera_botellas;
	cola corredor_esperando;
	cant=0;
	cola maquina;
	bool esperando_agua=false;
	
	procedure esperar (){
		cant++;
		if (cant=C){
			signaAll(espera_corredor);
		}
		else {
			wait(espera_corredor);
		}
	}
	
	procedure tomar_agua (in int id,out botella botella){
		if (corredor_esperando.isnotempty()){ // si hay gente esperando 
			push(corredor_esperando(id)); // me  encolo en la fila
			wait(espera_botella[id]); // espero a que me avise que cargo botellas
		}
		else {
			if(maquina.isempty){ // si no hay botellas
				signal(espera_repositor); // despierto al repositor
				push(corredor_esperando,id); // me encolo en la fila
				wait(esperar_botella[id]); // espero a que cargue botella
			}
		}
		pop(maquina,botella); // saco mi botella
		signal(espera_repositor); // le aviso que ya tome la botella
	}	
	
	procedure reponer_botellas (out cola maquina){
		if (corredor_esperando.isempty()){ // si no hay gente esperando me duermo
			wait(espera_repositor);
		}
		maquina=maquina; // me llevo la maquina
	}
	
	procedure confirmar_carga (in cola maquina_recargada){
		maquina=maquina_recargada; // cargo la maquina
		while (corredor_esperando.isnotempty) && (maquina.isnotempty){
		// mientras haya corredores esperando y maquina no este vacia	
			pop(corredor_esperando,id_aux); // saco corredor
			signal(espera_botella[id_aux]); // lo despierto
			wait(espera_repositor); // espero a que reciba su botella
		}
	}
}
