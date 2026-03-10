
10. 
	 A una cerealera van T camiones a descargarse trigo y M camiones a descargar maíz. Sólo hay lugar para que 7 camiones a la vez descarguen, pero no pueden ser más de 5 del mismo tipo de cereal. 
	 a) Implemente una solución que use un proceso extra que actúe como coordinador entre los camiones. El coordinador debe atender a los camiones según el orden de llegada. Además, debe retirarse cuando todos los camiones han descargado. 
	 b) Implemente una solución que no use procesos adicionales (sólo camiones). No importa el orden de llegada para descargar. Nota: maximice la concurrencia.


````c

	 a.
	 sem trigo = 5; 
	 sem maiz = 5;
	 sem deposito = 7;
	 sem termine = 0;
	 sem trigo [T] = ([T], 0);
	 sem maiz [M] = ([M], 0);
	 sem mutex = 1;
	 
	 
	 process trigo [id: 0..T-1]{
		 trigo=trigo
		 P(mutex)
		 c.push(id,trigo)
		 V(mutex)
		 V(llegue) 
		 P(trigo[id])
		 depositar(trigo);
		 V(deposito)
		 V(trigo)
		 V(termine)
	 } 
	 
	 
	 process maiz [id: 0..M-1]{
		 maiz=maiz
		 P(mutex)
		 c.push(id,maiz)
		 V(mutex)
		 V(llegue)
		 P(maiz[id])
		 depositar(maiz)
		 V(deposito)
		 V(maiz)
		 V(termine)
	 }
	 
	 process coordinador{
		 string tipo;
		 int id;
		 for i := 0 .. M+T-1 {
			 P(llegue)
			 P(mutex)
			 c.pop(id,tipo);
			 V(mutex)
			 if (tipo == "maiz"){
				 P(maiz)
				 P(deposito)
				 V(maiz[id]) 
			 }
			 else {
				 P(trigo)
				 P(deposito)
				 V(trigo[id])
			 }
		 }
		 for j := 0.. M+T-2{
			 P(termine)
		 }
	 }
	 
	 
	 b.
	 sem trigo = 5; 
	 sem maiz = 5;
	 sem deposito = 7;
	 
	 
	 process trigo [id: 0..T-1]{
		 trigo trigo;
		 P(trigo) // decremento la cantidad de tipo trigo
		 P(deposito) // decremento la cantidad de espacios libres para depositar
		 depositar(trigo) // deposito trigo
		 V(deposito) // incremento la cantidad de espacios libres para depositar
		 V(trigo) // incremento la cantidad de tipo trigo
	 } 
	 
	 
	 process maiz [id: 0..M-1]{
		 maiz maiz;
		 P(maiz) // decremento la cantidad de tipo maiz
		 P(deposito) // decremento la cantidad de espacios libres para depositar
		 depositar(maiz) // deposito maiz
		 V(deposito) // incremento la cantidad de espacios libres para depositar
		 V(maiz) // incremento la cantidad de tipo maiz
	 }
	 
	 