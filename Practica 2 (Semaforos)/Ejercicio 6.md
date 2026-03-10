6. 
	 Existen N personas que deben imprimir un trabajo cada una. Resolver cada ítem usando semáforos: 
	 a) Implemente una solución suponiendo que existe una única impresora compartida por todas las personas, y las mismas la deben usar de a una persona a la vez, sin importar el orden. Existe una función Imprimir(documento) llamada por la persona que simula el uso de la impresora. Sólo se deben usar los procesos que representan a las Personas. 
	 b) Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada. 
	 c) Modifique la solución de (a) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la impresora hasta que no haya terminado de usarla la persona X-1). 
	 d) Modifique la solución de (b) para el caso en que además hay un proceso Coordinador que le indica a cada persona que es su turno de usar la impresora. 
	 e) Modificar la solución (d) para el caso en que sean 5 impresoras. El coordinador le indica a la persona cuando puede usar una impresora, y cual debe usar.

````c

	 a.
	 sem mutex = 1
	 
	 Process persona [id: 0..N-1]{
		 P(mutex)
		 imprimir(ducumento);
		 V(mutex)
	}

	 b.
	 sem mutex = 1;
	 sem espera [N] = ([N] 0);
	 cola c;
	 bool libre = false;
	 
	 
	 Process persona [id: 0..N-1]{
		 P(mutex)
		 if (libre) then{
			 libre=false;
			 V(mutex);
		 }
		 else {
			 c.push(id);
			 V(mutex);
			 P (espera[id]);
		 }
		 imprimir (documento);
		 P(mutex)
		 if (!cola.isempty()) then{
			 V(espera[cola.pop()]);
		 }
		 else{
			 libre=true;
		 }
		 V(mutex);
	}

En este ejercicio se debe respetar el orden de llegada, por lo tanto hay que hacer atomico el proceso de la cola, este pregunta si la cola esta vacia, si esta vacia avanza directo a usar la impresora liberando la zona critica de la cola para que otra persona pueda usarla y encolarse.
El proceso que imprime cuando termina de imprimir tiene que bloquear la cola para sacar a uno y avisarle que puede seguir, en caso de que la cola este vacia este pone libre en true para que el proximo que entre pase directo.

	 c.
	 sem mutex = 1;
	 sem espera [N] = ([N] 0);
	 colaPrioridad c;
	 bool libre = false;
	 
	 
	 Process persona [id: 0..N-1]{
		 int aux;
		 P(mutex)
		 if (libre) then{
			 libre=false;
			 V(mutex);
		 }
		 else {
			 c.push(id);
			 V(mutex);
			 P (espera[id]);
		 }
		 imprimir (documento);
		 P(mutex)
		 if (!cola.isempty()) then{
			 c.pop(aux);
			 V(espera[aux]);
		 }
		 else{
			 libre=true;
		 }
		 V(mutex);
	}

En este ejercicio la diferencia clave es el tipo de la cola, ya que al ser del tipo colaPrioridad esta al hacer un push acomoda dependiendo el id (de menos a mayor). Por lo tanto al hacer un pop se va a sacar de la cola a la persona con el menor id.


	d.
	 sem mutex = 1;
	 sem espera [N] = ([N] 0);
	 cola c;
	 sem libre = 0;
	 sem llegada = 0;
	 
	 Process persona [id: 0..N-1]{
		 P(mutex)
		 c.push(id);
		 V(mutex)
		 V(llegada)
		 P(elemnto[id])
		 imprimir(documento);
		 V(listo)
	}

	 Process coordinador {
		 int id;
		 for i := 0 .. N-1 {
			 P(llegada)
			 P(mutex)
			 c.pop(id);
			 V(mutex)
			 V(turno[id])
			 P(listo)
		 }
	 }

En este ejercicio el coordinador facilita la carga de las impresiones, ya que es el el encargado de ir llamando a cada uno respetando el orden en el que llegan, esto como se hace? una persona le avisa llegue y este en un for de 0 hasta N-1 (es un for porque espera todos los V de las personas) va sacando de la cola a medida que llegan, como se asegura de que este termine antes de llamar a otro? ya que cuando despierta a uno el coordinador espera a que este le de diga que termino con un P(listo).