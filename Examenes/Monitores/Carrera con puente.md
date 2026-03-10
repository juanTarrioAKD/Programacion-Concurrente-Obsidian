
![[Tercer Anio/Concurrente/Imagenes/Imagen8.png]]


````c

process Corredor [id:1..C]{
	Carrera.inicio_carrera();
	--arranca la carrera
	Carrera.inicio_puente(id);
	--cruzando el puente (aprox 2 minutos);
	Carrera.fin_puente();
	--terminar carrera
}

Monitor Carrera {
	cond espera;
	cond espera_puente;
	bool libre = true;
	cola cola;
	
	procedure inicio_carrera(){
		cant++
		if (cant==C){
			signalall(espera);
		}
		else {
			wait(espera);
		}
	}
	
	procedure inicio_puente (in int id){
		if (!libre){
			push(cola(id))
			wait(espera_puente);
		}
		libre=false;
	}
	
	procedure fin_puente (){
		if (cola.isnotempty()){
			pop(cola(id));
			signal(espera_puente);
		}
		else {
			libre = true;
		}
	}
}


`````