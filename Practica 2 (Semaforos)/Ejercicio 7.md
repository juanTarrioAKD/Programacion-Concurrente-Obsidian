
7. 
	Suponga que se tiene un curso con 50 alumnos. Cada alumno debe realizar una tarea y existen 10 enunciados posibles. Una vez que todos los alumnos eligieron su tarea, comienzan a realizarla. Cada vez que un alumno termina su tarea, le avisa al profesor y se queda esperando el puntaje del grupo (depende de todos aquellos que comparten el mismo enunciado). Cuando un grupo terminó, el profesor les otorga un puntaje que representa el orden en que se terminó esa tarea de las 10 posibles.

````c

	 cola c;
	 sem mutex = 1;  // semaforo para bloqueo de variable cantiadad que llego
	 sem barrera = 0;  // semaforo para arrancar todos juntos 
	 int contador_llegada = 0;  // variable que cuenta la cantidad de alumnos que llegan
	 sem resultado ([10]) = ([10], 0);  // semaforo que avisa que el profesor ya corrigio el tema
	 vec notas[10]=([10], 0)  // variable compartida por tema
	 sem termine = 0;  // semaforo que avisa al profesor que un alumno termino
	
	 Process alumno [id: 0..n-1]{
		 tema = seleccionar_tema();
		 P(mutex)
		 contador_llegada++;
		 if (contador_llegada == 50){ 
			 for i:= 1 .. n-1{
				 V(barrera)  // libera a todos los procesos de la barrera
			 }
		 }
		 V(mutex) 
		 P(barrera) // todos se quedan aca esperando al ultimo
		 realizar_tarea(); 
		 P(mutex) // bloqueo la cola compartida
		 c.push(tema);
		 V(mutex)	// libero la cola compartida
		 V(termine)  // aviso al profe que termine
		 P(resultado[tema]) // espero el resultado
		 print ("mi nota es: notas[tema]")
	 }

	 Process profesor {
		 int tema;
		 int nota = 0;
		 int i;
		 int j;
		 for i := 0 .. 49{
			 P(termine) // espero que un alumno termine
			 P(mutex) // bloqueo la cola compartida
			 c.pop(tema)
			 V(mutex) // desbloqueo la cola compartida
			 notas [tema]++
			 if (notas[tema] == 5){
				 nota++
				 notas[tema]=nota;
				 for j := 0 .. 4 {
					 V(resultado[tema]) // despierto a los alumnos que estan esperando
				 }
			 }
		 }
	 }

