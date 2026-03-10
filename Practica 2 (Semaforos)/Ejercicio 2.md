2. 
	*Un sistema de control cuenta con 4 procesos que realizan chequeos en forma 
	colaborativa. Para ello, reciben el historial de fallos del día anterior (por simplicidad, de 
	tamaño N). De cada fallo, se conoce su número de identificación (ID) y su nivel de 
	gravedad (0=bajo, 1=intermedio, 2=alto, 3=crítico). Resuelva considerando las siguientes 
	situaciones: 
	a) Se debe imprimir en pantalla los ID de todos los errores críticos (no importa el 
	orden). 
	b) Se debe calcular la cantidad de fallos por nivel de gravedad, debiendo quedar los 
	resultados en un vector global. 
	c) Ídem b) pero cada proceso debe ocuparse de contar los fallos de un nivel de 
	gravedad determinado.*
	
````c
	a y b.
	
	array vec;
	array contador [4] = ([4] 0);
	sem mutex = 1;
	
	process proceso ([id : 1..4]){
		array vecLocal [4] = ([4] 0);
		for i:= ((id-1)*dimL/4) to ((id*(dimL/4))-1){
			if (vec[i].nivel_gravedad == 3){
				writeln (vec[i].numero_identificacion);
			}
			vecLocal[vec[i].nivel_gravedad]++;
		}
		for j:= 0 to 3{
			p(mutex)
			contador[j]+=vecLocal[j];
			v(mutex)
		}	
	}	
	
	
	c.
	
	array vec;
	array contador [4] = ([4] 0);
	
	process proceso ([id : 1..4]){
		int cant=0;
		for i:= ((id-1)*dimL/4) to ((id*(dimL/4))-1){
			if (vec[i].nivel_gravedad == id_aContar){
				cant++;
			}
		}
		contador[j]+=cant;	
	}	
	
````

