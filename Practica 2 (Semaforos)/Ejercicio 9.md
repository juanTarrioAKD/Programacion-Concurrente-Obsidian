
9. 
	 Resolver el funcionamiento en una fábrica de ventanas con 7 empleados (4 carpinteros, 1 vidriero y 2 armadores) que trabajan de la siguiente manera: • Los carpinteros continuamente hacen marcos (cada marco es armando por un único carpintero) y los deja en un depósito con capacidad de almacenar 30 marcos. • El vidriero continuamente hace vidrios y los deja en otro depósito con capacidad para 50 vidrios. • Los armadores continuamente toman un marco y un vidrio (en ese orden) de los depósitos correspondientes y arman la ventana (cada ventana es armada por un único armador).

````c

	 // variables para el manejo de marcos
	 sem vacioMarco = 30
	 sem llenoMarco = 0
	 sem mutexMarco = 1
	 array despositoMarco [30] = ([30], 0)
	 //variables para el manejo de vidrios
	 sem vacioVidrio = 50
	 sem llenoVidrio = 0
	 sem mutexVidrio = 1
	 array depositoVidrio [50] = ([50], 0)
	 //variables para el manejo de armado
	 sem mutexArmado = 1;

	 
	 process carpintero [id: 1..4]{
		 marco marco;
		 int pos;
		 while (true){
			 marco=armar_marco();
			 P(vacioMarco) // semaforo que traba si el deposito marco esta lleno
			 P(mutexMarco) // semaforo que bloquea deposito
			 posicion_vaciaMarco(pos); // me retorna la posicion vacia del deposito
			 depositoMarco[pos]=marco; // deposito el marco 
			 V(mutexMarco) // libero el deposito de marcos
			 V(llenoMarco) // incremento el numero de marcos
		 }	 
	 
	 }
	 	 
	 
	 process vidriero {
		 vidrio vidrio;
		 int pos;
		 while (true)
			 vidrio=armar_vidrio() 
			 P(vacioVidrio) // semaforo que traba si esta lleno el deposito de vidrio
			 P(mutexVidrio) // bloqueo el deposito de vidrio
			 posicion_vacioVidrio(pos); // obtengo posicion vacia
			 depositoVidrio(pos); // deposito el vidrio
			 V(mutexVidrio) // libero el deposito de vidrio
			 V(llenoVidrio) // incremento la cantidad de vidrio
	 }
	 
	 
	 process armador [id: 1..2]{
		 marco marco;
		 vidrio vidrio;
		 while (true){
			 P(llenoMarco) // espero hasta tomar un marco
			 P(mutexMarco) // bloqueo el deposito de marcos
			 tomar_marco(marco) // retorna un marco del deposito de marcos
			 V(vacioMarco) // aviso que saque un marco
			 V(mutexMarco) // libero el deposito de marcos
			 P(llenoVidrio) // espero hasta tomar un vidrio
			 P(mutexVidrio) // bloqueo el deposito de vidrios
			 tomar_vidrio(vidrio) // retorna un vidrio del deposito de vidrios
			 P(vacioVidrio) // aviso que saque un vidrio
			 V(mutexVidrio) // libero el deposito de virios
			 p(mutexArmado) // bloqueo la maquina de armados
			 armar(marco,vidrio) // armo la ventana
			 V(mutexArmado) // libero la maquina de armados
		 }
	 }
	 