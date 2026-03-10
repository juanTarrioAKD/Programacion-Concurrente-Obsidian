
8. 
	 Una fábrica de piezas metálicas debe producir T piezas por día. Para eso, cuenta con E empleados que se ocupan de producir las piezas de a una por vez. La fábrica empieza a producir una vez que todos los empleados llegaron. Mientras haya piezas por fabricar, los empleados tomarán una y la realizarán. Cada empleado puede tardar distinto tiempo en fabricar una pieza. Al finalizar el día, se debe conocer cual es el empleado que más piezas fabricó. a) Implemente una solución asumiendo que T > E. b) Implemente una solución que contemple cualquier valor de T y E.

````c

	 int cant = T; // variable que cuenta cuantas piezas se tomaron
	 int cant_emp = 0;
	 sem terminamos = 0; //sem que avisa a la fabrica que el ultimo empleado llego
	 sem barrera = 0; // sem que despierta a todos
	 int maxCant = 0; // mantiene la cantidad maxima 
	 int maxEmpelado = 0; // mantiene el empleado maximo
	 
	 
	 Process empleado [id: 0..E-1]{
		 int cant_producidas_empelado=0;
		 // proceso de llegada
		 P(mutex)
		 cant_emp++
		 if (cant_emp == E){
			 for i:= 0 .. E-1{
				 V(barrera)
			 }
		 }
		 V(mutex)
		 P(barrera)
		 // proceso de fabricacion
		 P(mutex)
		 while (cant >= 1){
			 tomar_pieza();
			 cant--
			 V(mutex)
			 fabricar_pieza();
			 cant_producidas_empleado++
			 P(mutex)
		 }
		 V(mutex)
		 P(maximo)
		 if (cant_producidas_empleado > max){
			 maxCant=cant_producidas_empleado;
			 maxEmpleado=id;
		 }
		 V(maximo)
		 P(mutex)
		 cant--
		 if (cant = 0){
			 V(terminamos)
		 }
		 V(mutex)
	 }

	 
	 Process fabrica {
		 P(terminamos)
		 printeln("el empleado que mas piezas fabrico es:" maxEmpleado, "con una cantidad de: " maxCant);
	 }
	 