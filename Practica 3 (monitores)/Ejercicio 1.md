
1. 
	 Se dispone de un puente por el cual puede pasar un solo auto a la vez. Un auto pide permiso para pasar por el puente, cruza por el mismo y luego sigue su camino. 
	 a. ¿El código funciona correctamente? Justifique su respuesta. 
	 b. ¿Se podría simplificar el programa? ¿Sin monitor? ¿Menos procedimientos? ¿Sin variable condition? En caso afirmativo, rescriba el código. 
	 c. ¿La solución original respeta el orden de llegada de los vehículos? Si rescribió el código en el punto b), ¿esa solución respeta el orden de llegada?

````c

	 a.
	 El ejercicio presenta un error en el pasaje de parametros, ya que en el proceso   auto llama a procesos que si existen dentro del monitor pero no reciben ningun parametro y el auto le envia "a".

	 b.
	Se prodria simplificar ya que los monitores resuelven por default con exclusion mutuo, no hace falta agregarle codigo como el while para consultar si esta ocupado, los monitores poseen un tipo de dato llama cond que al dormirlo espera que algun otro proceso lo despierte. Por lo tanto no hace falta un while durmiendo constantemente.
	
	 Monitor Puente
		cond cola;
		int cant = 0;
		
		Procedure entrar_puente (){
			if (ocupado){
				await(cola);
			}
			ocupado=true;
		}
		
		Procedure salir_puente (){
			ocupado=false;
			if (cola.isnotempty()){
				await(cola);
			}
		}
	
	Process Auto (){
		puente.entrar_puente();
		pasar_puente();
		puente.salir_puente();
	}


	 c.
	 Ambas soluciones respetan el orden de llegada.
````
