
12. 
	Simular la atención en una Terminal de Micros que posee 3 puestos para hisopar a 150 pasajeros. En cada puesto hay una Enfermera que atiende a los pasajeros de acuerdo con el orden de llegada al mismo. Cuando llega un pasajero se dirige al Recepcionista, quien le indica qué puesto es el que tiene menos gente esperando. Luego se dirige al puesto y espera a que la enfermera correspondiente lo llame para hisoparlo. Finalmente, se retira. 
	a) Implemente una solución considerando los procesos Pasajeros, Enfermera y Recepcionista.
	b) Modifique la solución anterior para que sólo haya procesos Pasajeros y Enfermera, siendo los pasajeros quienes determinan por su cuenta qué puesto tiene menos personas esperando. Nota: suponga que existe una función Hisopar() que simula la atención del pasajero por parte de la enfermera correspondiente.


````c

	 array colas [3] = ([3], 0) (son 3 colas) 	
	 int cantPasajero = 150; 
	 sem mutexPasajero = 1;
	 sem mutexEnfermera = 1;
	 sem retirarse [150] = ([150], 0)
	 
	 process pasajero [id: 0..149]{
		 P(mutexPasajero)
		 c.pop(id);
		 V(llegue)
		 V(mutexPasajero)
		 P(retirarse[id]) 
		 V(?)
	 }

	 process enfermera [id:0..2]{
		 int id_pasajero;
		 P(muetexCantPasajeros)
		 while (cantPasajero>0){
			 cantPasajeros--
			 V(mutexCantPasajeros)
			 P(mutexAviso[id])
			 P(mutexEnfermera[id])
			 colas[id].pop(id_pasajero)
			 P(mutexEnfermera[id])
			 hispopar(id_pasajero);
			 P(mutexCantPasajeros)
			 V(retirarse[id_pasajero])
			 P(?)
		 }
		 V(mutexCantPasajeros)
	 }

	 process recepcionista {
		 array contador [3] = ([3],0);
		 int id_pasajero;
		 int id_cola;
		 for i:= 1..150{
			 P(mutexPasajero)
			 c.pop(id_pasajero);
			 V(mutexPasajero)
			 obtener_menor(id_cola);
			 P(mutexEnfermera[id_cola])
			 colas[id_cola].push(id_pasajero);
			 V(mutexEnfermera[id_cola])
			 V(mutexAviso[id_cola]) 
		 }
	 }

