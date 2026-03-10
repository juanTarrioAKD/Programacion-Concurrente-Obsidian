
2. 
	 Existen N procesos que deben leer información de una base de datos, la cual es administrada por un motor que admite una cantidad limitada de consultas simultáneas. 
	 a) Analice el problema y defina qué procesos, recursos y monitores/sincronizaciones serán necesarios/convenientes para resolverlo. 
	 b) Implemente el acceso a la base por parte de los procesos, sabiendo que el motor de base de datos puede atender a lo sumo 5 consultas de lectura simultáneas.

	 
	````c
	
	 a. 
	 Por lo leido en el enunciado voy a necesitar un process query [1..N] que van a acceder a un monitor de una base de datos. Este monitor va a manejar los accesos a la base de datos.

	 b.
	 Monitor BaseDeDatos 
		int cant_leyendo = 0;
		int cant_espera = 0;
		cond espera;
		
		procedure  acces_database(){
			if (cant_leyendo<5){
				cant_leyendo++;
			}
			else {
				cant_espera++:
				wait(espera);
			}
		}

		procedure liberar_database(){
			if (cant_espera>0){
				cant_espera--;
				signal(espera);
			}
			else {
				cant_leyendo--
			}
		}

	Process Query [id: 1..N]{
	BaseDeDatos.acces_database();
	--realizo lectura
	BaseDeDatos.liberar_database();
	}
````

