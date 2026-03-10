
1.  
	*Existen N personas que deben ser chequeadas por un detector de metales antes de poder 
	ingresar al avión.  
	a. Analice el problema y defina qué procesos, recursos y semáforos/sincronizaciones 
	serán necesarios/convenientes para resolverlo. 
	b. Implemente una solución que modele el acceso de las personas a un detector (es decir, 
	si el detector está libre la persona lo puede utilizar; en caso contrario, debe esperar).  
	c. Modifique su solución para el caso que haya tres detectores.  
	d. Modifique la solución anterior para el caso en que cada persona pueda pasar más de 
	una vez, siendo aleatoria esa cantidad de veces.* 
	**

````c

	a. En el problema lo primero que se identifica es un semaforo para el manejo del detector (con valor a 1 para el primer caso
	y con valor 3 para los otros casos). Y un proceso persona que va a utilizar simultaneamente el recurso del detector.
	
	
	b. 
	
	sem mutex = 1;  // tengo 1 solo detector disponible
	
	process persona ([id: 0..N]){
		
		p (mutex) 						// bloqueo el recurso para poder utilizarlo                                          (decremento el valor de mutex en 1 dejandolo en 0)
			//escanearse 
		v (mutex)						// libero el recurso para que otro lo utilice                                             (aumento en 1 el valor de mutex dejandolo en 1)
		//subirse al avion	
	}
	
	
	c. 
	
	sem mutex = 3;  // tengo 3 detectores disponibles
	
	process persona ([id: 0..N]){
		
		p (mutex) 						// bloqueo el recurso para poder utilizarlo                                         (decremento el valor de mutex en 1 dejandolo en 0)
			//escanearse 
		v (mutex)						// libero el recurso para que otro lo utilice                                            (aumento en 1 el valor de mutex dejandolo en 1)
		//subirse al avion	
	}
	
	
	d. 
	
	sem mutex = 3;  // tengo 3 detectores disponibles
	
	process persona ([id: 0..N]){
		for i: 1 to Random(N){				// cada persona pasa N veces por el                                                          detector
			p (mutex) 			// bloqueo el recurso para poder  utilizarlo                                                 (decremento el valor de mutex en 1 dejandolo en 0)
			//escanearse 
			v (mutex)            // libero el recurso para que otro lo utilice                                                 (aumento en 1 el valor de mutex dejandolo en 1)
			//subirse al avion
		}		
	}
-------------------------------------------------------------------------------

