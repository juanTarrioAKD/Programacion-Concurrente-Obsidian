
5. 
	 En una empresa de logística de paquetes existe una sala de contenedores donde se preparan las entregas. Cada contenedor puede almacenar un paquete y la sala cuenta con capacidad para N contenedores. Resuelva considerando las siguientes situaciones: 
	 a) La empresa cuenta con 2 empleados: un empleado Preparador que se ocupa de preparar los paquetes y dejarlos en los contenedores; un empelado Entregador que se ocupa de tomar los paquetes de los contenedores y realizar la entregas. Tanto el Preparador como el Entregador trabajan de a un paquete por vez. 
	 b) Modifique la solución a) para el caso en que haya P empleados Preparadores. 
	 c) Modifique la solución a) para el caso en que haya E empleados Entregadores. 
	 d) Modifique la solución a) para el caso en que haya P empleados Preparadores y E empleadores Entregadores.

````c

	 a.
	 array contenedores = [(N) 0]
	 sem vacio = N 
	 sem lleno = 0
	 sem contenido = 0
	 
	 Process empleadoPreparador{
		 paquete p;
		 while (true){
			 p = -- preparo paquete
			 P(vacio)
			 contenedores.llenarprox(p);
			 V(lleno)
		 }
	 }

	 Processo empleadoEntregador{
		 paquete p;
		 while (true){
			 P(lleno)
			 p = contenedores.ultimopaquete();
			 V(vacio)
		 }
	 }

	 d.
	 array contenedores = [(N) 0]
	 sem vacio = N 
	 sem lleno = 0
	 sem mutexP = 1
	 sem mutexE = 1
	 
	 Process empleadoPreparador[id: 0..P-1]{
		 paquete p;
		 while (true){
			 p = -- preparo paquete
			 P(mutexP)
			 P(vacio)
			 contenedores.llenarprox(p);
			 V(lleno)
			 P(mutexP)
		 }
	 }

	 Processo empleadoEntregador[id: 0..P-1]{
		 paquete p;
		 while (true){
			 P(lleno)
			 P(mutexE)
			 p = contenedores.ultimopaquete();
			 V(mutexE)
			 V(vacio)
		 }
	 }

