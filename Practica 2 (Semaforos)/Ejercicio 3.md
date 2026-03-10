3. 
	 Un sistema operativo mantiene 5 instancias de un recurso almacenadas en una cola. Además, existen P procesos que necesitan usar una instancia del recurso. Para eso, deben sacar la instancia de la cola antes de usarla. Una vez usada, la instancia debe ser encolada nuevamente para su reúso.

````c

	 queue cola   //contiene los 5 recursos
	 sem mutex = 1
	 
	 Process sistemaOperativo [id: 0..P-1]{
		 r recurso;
		 P(mutex)
		 r = cola.desencola();
		 V(mutex)
		 -- utilizo recurso
		 P(mutex)
		 cola.encolar(r);
		 V(mutex)
	 }

