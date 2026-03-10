
3. 
	Existen N personas que deben fotocopiar un documento. La fotocopiadora sólo puede ser usada por una persona a la vez. Analice el problema y defina qué procesos, recursos y monitores serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema. Luego, resuelva considerando las siguientes situaciones: 
	a) Implemente una solución suponiendo no importa el orden de uso. Existe una función Fotocopiar() que simula el uso de la fotocopiadora. 
	b) Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada. 
	c) Modifique la solución de (b) para el caso en que se deba dar prioridad de acuerdo con la edad de cada persona (cuando la fotocopiadora está libre la debe usar la persona de mayor edad entre las que estén esperando para usarla). 
	d) Modifique la solución de (a) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la fotocopiadora hasta que no haya terminado de usarla la persona X-1). 
	e) Modifique la solución de (b) para el caso en que además haya un Empleado que le indica a cada persona cuando debe usar la fotocopiadora. 
	f) Modificar la solución (e) para el caso en que sean 10 fotocopiadoras. El empleado le indica a la persona cuál fotocopiadora usar y cuándo hacerlo.


````c
	 b.
	 Process persona [id: 1..N]{
		Copy copy;
		impresora.solicitar();
		fotocopiar(copy);
		impresora.liberar();
	 }
	 
	 Monitor impresora 
		bool ocupado = false;
		int cant_esperando = 0;
		cond espera;
		
		procedure solicitar (){
			if (ocupado){
				cant_esperando++
				wait(espera);
			}
			ocupado=true;
		}
		
		procedure liberar (){
			if (cant_esperando>0){
				cant_esperando--;
				signal(espera);
			}
			else {
				ocupado=false;
			}
		}

----------------------------------------------------------------------------

	 c.
	 Process persona [id: 1..N]{
		Copy copy;
		int edad;
		 obtener_edad(edad);
		impresora.solicitar(id,edad);
		fotocopiar(copy);
		impresora.liberar();
	 }
	
	 Monitor impresora 
		bool ocupado = false;
		int cant_esperando = 0;
		int aux;
		cond espera[N];
		colaPriotidad cola;
		
		procedure solicitar (in int id, in int edad){
			if (ocupado){
				push(c,id,edad);
				wait(espera[id]);
			}
			ocupado=true;
		}
		
		procedure liberar (){
			if (c.isnotempty()){
				pop(c,aux)
				signal(esperar[aux]);
			}
			else {
				ocupado=false;
			}
		}

----------------------------------------------------------------------------

	d.
	 Process persona [id: 1..N]{
		Copy copy;
		impresora.solicitar();
		fotocopiar(copy);
		impresora.liberar();
	 }
	
	 Monitor impresora 
		 bool vector [1..N] = ([N],false)
		 int prox_truno = 1;
		 cond espera[N];
		
		procedure solicitar (in int id){
			if (id<>prox_turno){
				 vector[id]=true;
				 await(espera[id]);
			}
		}
		
		procedure liberar (){
			prox_turno++;
			if (vector[prox_turno]){
				signal(esperar[prox_turno]);
			}
		}

----------------------------------------------------------------------------

	e.
	 Process persona [id: 1..N]{
		Copy copy;
		impresora.solicitar();
		fotocopiar(copy);
		impresora.liberar();
	 }
	 
	 Process coordinador{
		 while (true){
			 impresora.dejar_pasar()
		 }
	 }
	 
	 Monitor impresora 
		bool ocupado = false;
		int cant_esperando = 0;
		cond espera_persona[N];
		cond espera_coordinador;
		 int aux;
		
		procedure solicitar (in int id){
			if(c.isempty()){
				signal(espera_coordinador);
			}
			push(c,id);
			await(espera_persona[id]);
		}
		
		procedure liberar (){
			signal(espera_coordiandor)
		}
		
		procedure dejar_pasar(){
			if(c.isnotempty()){
				 pop(c,aux)
				 signal(espera_actual[aux]); 
			}
			 wait(espera_coordinador);
		}
````

